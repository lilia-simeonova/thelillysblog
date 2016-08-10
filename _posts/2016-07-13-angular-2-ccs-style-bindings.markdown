---
layout: post
title:  "Angular 2 CCS Style Bindings"
date:   2016-07-13 09:00:13
categories: Angular2
---




In this post we will take a look on how to bind different styles to DOM elements in Angular 2.

You are all familiar with the following markup, right?

{% raw %}
``` javascript
<div style="color:red;">Angular 2 is awesome</div>
```
Now, let’s put some Angular 2 sugar on it:

``` javascript
<div [ngStyle] = "{color: "red";}">Angular 2 is awesome</div>
```
Ok, it does absolutely the same thing as a simple html style. So why we should bother using it?

There is something called Angular 2 Directives, which helps us get some dynamic in our front-end. Now is the time to mention that there are 3 types of directives:

* Components
* Structural directives
* Attribute directives

In our example we used the attribute directives. Their purpose is to change the behavior of any DOM element. And that is exactly what we need.

Imagine if you don’t want your color always to be red, in some conditions you want it to be green. Here the directive comes in. We simply need to put a variable instead of particular color.

    <div [ngStyle] = "{color: color;}">Angular 2 is awesome</div>

So far, so good. But you may wonder from where this variable comes in.

If you are familiar with the basic structure of Angular 2 application, you should know that we should have a basic component, like the one below:

```javascript
@Component({
  selector: 'test-styles',
  templateUrl: 'app/app.component.html'
})
export class AppComponent {
  //you can either define myColor in the class
  public myColor: string = ‘red’;
  //or you can create a function to define it
  getColor() {
    return 'green';
  }
}
```
And here is how app/app.component.html looks like:

``` javascript
<div style="color: red">Angular 2 is awesome</div>
<div [ngStyle] = "{color: "red"}">Angular 2 is awesome</div>
<div [ngStyle] = "{color: "myColor"}">Angular 2 is awesome</div>
<div [ngStyle] = "{color: "getColor()"}">Angular 2 is awesome</div>
```
In browser the final result would be:

<div style="color: red">Angular 2 is awesome</div>
<div style = "color: red">Angular 2 is awesome</div>
<div style = "color: red">Angular 2 is awesome</div>
<div style = "color: green">Angular 2 is awesome</div>

Angular 2 is also allowing us to use directives directly with CCS styles, so the following statement is valid one:

``` javascript
<div [style.color] = "red">Angular 2 is awesome</div>
```
Have you seen how much time we just saved?

Angular 2 is indeed awesome!
{% endraw %}