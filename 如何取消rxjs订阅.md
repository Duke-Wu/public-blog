# 如何取消rxjs订阅

#### 1. 手动取消订阅

```typescript
let sub = this.props$.subscribe();

// 当注销时取消订阅
sub.unsubscribe();
```

逻辑简单，需要额外引入变量保存，代码量大和维护难！

#### 2. takeUntil Or takeWhile

takeUntil: 发出值，直到提供的 observable 发出值，它便完成。

```typescript
// 示例：取值直到 timer 发出
// RxJS v6+
import { interval, timer } from 'rxjs';
import { takeUntil } from 'rxjs/operators';

// 每1秒发出值
const source = interval(1000);
// 5秒后发出值
const timer$ = timer(5000);
// 当5秒后 timer 发出值时， source 则完成
const example = source.pipe(takeUntil(timer$));
// 输出: 0,1,2,3
const subscribe = example.subscribe(val => console.log(val));

```

takeWhile: 发出值，直到提供的表达式结果为 false 。

```typescript
// 示例: 使用限定条件取值
// RxJS v6+
import { of } from 'rxjs';
import { takeWhile } from 'rxjs/operators';

// 发出 1,2,3,4,5
const source = of(1, 2, 3, 4, 5);
// 允许值发出直到 source 中的值大于4，然后便完成
const example = source.pipe(takeWhile(val => val <= 4));
// 输出: 1,2,3,4
const subscribe = example.subscribe(val => console.log(val));

```



#### 3. 总结

complete 和 error 触发后，unsubscribe 也会自动调用

当 next、complete和error 出现异常时，unsubscribe 也会自动调用以保证资源不会浪费

next、complete和error是可选的。按需处理即可，不必全部处理