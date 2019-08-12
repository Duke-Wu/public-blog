---
title: How to upload the SAME file in angular4
date: 2019/7/22 11:01:0
updated: 2019/7/22 11:01:0
comments: true
tags:
- angular
- file upload
categories:
- tool kits
---

# How to upload the SAME file in angular4

原问题：https://stackoverflow.com/questions/49976714/how-to-upload-the-same-file-in-angular4

I was able to successfully upload a file, but the problem now is it won't let me upload the same file twice. Here's my code:

```html
 <input type="file" (change)="onFileChange($event)" name="fileUploaded" value="Choose a File" accept=".xlsx, .xlsm">
```

I found out that the onFileChange($event) won't fire since the listener I used is (change) and I'm not changing the file the I'm uploading. What can I do to sovle my problem? Thank you

<!--more-->
More: To give you context why would I want to do that, I want to upload an excel file then display its content to the page. But when I upload an excel file then edit its data inside using ms excel then save and upload it again, the new edits won't display on the page. The page still displays the old data.


## Solution 1

You can reference and then clear your input:

**template**

```js
<input #myFileInput type="file"/>
```

**script**

```js
@ViewChild('myFileInput') myFileInput;

onFileChange(event) { 
  this.myFileInput.nativeElement.value = '';
}
```



## Soution 2

> Change event function not working on input type file if file name is same
>
> 原问题：https://stackoverflow.com/questions/46970952/change-event-function-not-working-on-input-type-file-if-file-name-is-same

In angular 2 you can do it like this:

```html
<span class="control-fileupload" >
      <label for="file1" class="text-left">{{filePlaceHolder}}</label>
      <input #fileInput type="file" id="file1" (click)="fileInput.value = null" value="" (change)="openFile($event)" >
 </span>
```

This way every time you click on file input it will clear it's value so even if you select the same file change will fire.



## Others 

How to upload same file twice? 

https://github.com/nervgh/angular-file-upload/issues/542

**angular-file-upload FAQ**
https://github.com/nervgh/angular-file-upload
