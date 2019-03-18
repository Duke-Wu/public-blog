Angular 的口号是 - “一套框架，多种平台。同时适用手机与桌面 (One framework.Mobile & desktop.)”，即 Angular 是支持开发跨平台的应用，比如：Web 应用、移动 Web 应用、原生移动应用和原生桌面应用等。

为了能够支持跨平台，Angular 通过抽象层封装了不同平台的差异，统一了 API 接口。如定义了抽象类 Renderer 、抽象类 RootRenderer 等。此外还定义了以下引用类型：ElementRef、TemplateRef、ViewRef 、ComponentRef 和 ViewContainerRef 等。

下面我们就来分析一下 ElementRef 类：

**ElementRef 的作用**

在应用层直接操作 DOM，就会造成应用层与渲染层之间强耦合，导致我们的应用无法运行在不同环境，如 web worker 中，因为在 web worker 环境中，是不能直接操作 DOM。有兴趣的读者，可以阅读一下 [Web Workers 中支持的类和方法][1] 这篇文章。通过 ElementRef 我们就可以封装不同平台下视图层中的 native 元素 (在浏览器环境中，native 元素通常是指 DOM 元素)，最后借助于 Angular 提供的强大的依赖注入特性，我们就可以轻松地访问到 native 元素。

**ElementRef 的定义**

```javascript
class ElementRef<T> {
  constructor(nativeElement: T)
  nativeElement: T
}
```



**ElementRef 的应用**

我们先来介绍一下整体需求，我们想在页面成功渲染后，获取页面中的 div 元素，并改变该 div 元素的背景颜色。接下来我们来一步步，实现这个需求。

首先我们要先获取 div 元素，在文中 “ElementRef 的作用” 部分，我们已经提到可以利用 Angular 提供的强大的依赖注入特性，获取封装后的 native 元素。在浏览器中 native 元素就是 DOM 元素，我们只要先获取 my-app元素，然后利用 querySelector API 就能获取页面中 div 元素。具体代码如下：

```javascript
import { Component, ElementRef } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `
    <h1>Welcome to Angular World</h1>
    <div>Hello {{ name }}</div>
  `,
})
export class AppComponent {
  name: string = 'Semlinker';

  constructor(private elementRef: ElementRef) {
    let divEle = this.elementRef.nativeElement.querySelector('div');
    console.dir(divEle);
  }
}
```

运行上面代码，在控制台中没有出现异常，但是输出的结果却是 null 。什么情况 ？ 没有抛出异常，我们可以推断 this.elementRef.nativeElement 这个对象是存在，但却找不到它的子元素，那应该是在调用构造函数的时候，my-app 元素下的子元素还未创建。

那怎么解决这个问题呢 ？沉思中… ，不是有 setTimeout 么，我们在稍微改造一下：

```javascript
constructor(private elementRef: ElementRef) {
  setTimeout(() => { // 此处需要使用箭头函数哈，你懂的...
      let divEle = this.elementRef.nativeElement.querySelector('div');
      console.dir(divEle);
   }, 0);
}
```

更新一下代码，此时控制台成功输出了 div 。为什么添加个 setTimeout 就能成功获取到想要的 div 元素呢？此处就不展开了，有兴趣的读者可以参考 - [What the heck is the event loop anyway?][2] 这个演讲的示例。

问题解决了，但感觉不是很优雅 ？有没有更好的方案，答案是肯定的。Angular 不是有提供组件生命周期的钩子，我们可以选择一个合适的时机，然后获取我们想要的 div 元素。

```javascript
import { Component, ElementRef, AfterViewInit } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `
    <h1>Welcome to Angular World</h1>
    <div>Hello {{ name }}</div>
  `,
})
export class AppComponent {

  name: string = 'Semlinker';

  // 在构造函数中 this.elementRef = elementRef 是可选的，编译时会自动赋值
  // function AppComponent(elementRef) { this.elementRef = elementRef; }
  constructor(private elementRef: ElementRef) { } 

  ngAfterViewInit() { // 模板中的元素已创建完成
    console.dir(this.elementRef.nativeElement.querySelector('div'));
    // let greetDiv: HTMLElement = this.elementRef.nativeElement.querySelector('div'); 
    // greetDiv.style.backgroundColor = 'red';
  }
}
```

运行一下上面的代码，我们看到了意料中的 div 元素。我们直接选用 ngAfterViewInit 这个钩子，不要问我为什么，因为它看得最顺眼咯。不过我们后面也会有专门的文章，详细分析一下 Angular 组件的生命周期。成功取到 div 元素，就剩下的事情就好办了，直接通过 style 对象设置元素的背景颜色。

功能虽然已经实现了，但还有优化的空间么？当然有咯！其实在 Angular 框架内部已经为我们提供了解决方案，它为我们提供了内置的装饰器，如 @ContentChild、 @ContentChildren、@ViewChild、@ViewChildren 等。

具体使用示例如下：

```javascript
import { Component, ElementRef, ViewChild, AfterViewInit } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `
    <h1>Welcome to Angular World</h1>
    <div #greet>Hello {{ name }}</div>
  `,
})
export class AppComponent {
  name: string = 'Semlinker';

  @ViewChild('greet')
  greetDiv: ElementRef;

  ngAfterViewInit() {
    this.greetDiv.nativeElement.style.backgroundColor = 'red';
  }
}
```

是不是感觉瞬间高大上了，不过先等等，上面的代码是不是还有进一步的优化空间呢 ？我们看到设置 div 元素的背景，我们是默认应用的运行环境在是浏览器中。前面已经介绍了，我们要尽量减少应用层与渲染层之间强耦合关系，从而让我们应用能够灵活地运行在不同环境。

最后我们来看一下，最终优化后的代码：

```javascript
import { Component, ElementRef, ViewChild, AfterViewInit, Renderer2 } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `
    <h1>Welcome to Angular World</h1>
    <div #greet>Hello {{ name }}</div>
  `,
})
export class AppComponent {
  name: string = 'Semlinker';

  @ViewChild('greet')
  greetDiv: ElementRef;

  constructor(private elementRef: ElementRef, private renderer: Renderer2) { }

  ngAfterViewInit() {
    // this.greetDiv.nativeElement.style.backgroundColor  = 'red';
    this.renderer.setStyle(this.greetDiv.nativeElement, 'backgroundColor', 'red');
  }
}
```

最后我们通过 Renderer2 实例提供的 API 优雅地设置了 div 元素的背景颜色。

### 我有话说

Renderer2 API 还有哪些常用的方法 ？

```javascript
export abstract class Renderer2 {
  abstract createElement(name: string, namespace?: string|null): any;
  abstract createComment(value: string): any;
  abstract createText(value: string): any;
  abstract setAttribute(el: any, name: string, value: string,
    namespace?: string|null): void;
  abstract removeAttribute(el: any, name: string, namespace?: string|null): void;
  abstract addClass(el: any, name: string): void;
  abstract removeClass(el: any, name: string): void;
  abstract setStyle(el: any, style: string, value: any, 
    flags?: RendererStyleFlags2): void;
  abstract removeStyle(el: any, style: string, flags?: RendererStyleFlags2): void;
  abstract setProperty(el: any, name: string, value: any): void;
  abstract setValue(node: any, value: string): void;
  abstract listen(
      target: 'window'|'document'|'body'|any, eventName: string,
      callback: (event: any) => boolean | void): () => void;
}
```



