---
layout: post
title:  "Angular 2 Modules - the breaking changes in RC5"
date:   2016-08-17 10:00:13
categories: Angular
---

# Modules

You can think of Angular 2 Modules as some sort of containers that wrap our application.

If you are just starting to learn Angular 2, you will find this very intuitive, if not ...

<img src="{{ site.baseurl }}/images/QKl50Nv.gif">

In our application we should have a root module, called `AppModule`, cointaining all of the required information that we need to run the app.

In this module we need to import and declare all of the directives and pipes that we will going to use in the components binded to it.

``` javascript
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent }  from './app.component';

@NgModule({
  imports: [ BrowserModule ],
  declarations: [ AppComponent ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { }
```

### BrowserModule

This module registers service providers and includes directives such as `NgIf` and `NgFor` (which we used to use without importing anything, so be careful not to forget that)

### @NgModule Decorator

With this decorator we introduce services that can be used by any component, we also can wrap up some components, decorators and pipes together and import other modules as well.

Let's take a look at what we have so far:

First we declared our `AppModule`.

The next thing is to create our `AppComponent`, where nothing special happens.

``` javascript
import { Component } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `
    <h2>Hello buddy!</h2>
  `
})
export class AppComponent {}
```

## Add another component

Whe you want to extend your application and add another component, you need to do 3 things:

1. To import your new component in the **app component**
2. To import your new component in the **app module** 
3. To declare it in @NgModule decorator

Now, we are going to create `FormComponent`, doing the following:

<img src="{{ site.baseurl }}/images/formComponent.gif">

Back in our 'App Component' the only difference is importing the FormComponent:

``` javascript
import { Component } from '@angular/core';
import { FormComponent } from 'app/form.component'

@Component({
  selector: 'my-app',
  template: `
    <h2>Hello buddy!</h2>
  `
})
export class AppComponent {}
```

The FormComponent looks like this:

``` javascript
import { Component } from '@angular/core';

@Component({
  selector: 'my-form',
  template: `
      <div>What is your name?</div><br>
      //Here we are using two-way binding of the name property to the input
      <input type="text" [(ngModel)]="name">
      <br><br>
      //NgIf directive will show the div below based on whether the name property 
      //has a value or not
      <div *ngIf='name'>Nice to meet you, {{ name }}!</div>
  `
})
export class FormComponent {
 public name: string;
}
```

However if you only create this FormComponent and update the AppComponent file, your application will not work.

We need to state that we are going to use our FormComponent in the `App Module` by importing it the same way as the `AppComponent` and declare it in `declarations`:

``` javascript
import { NgModule }       from '@angular/core';
import { BrowserModule }  from '@angular/platform-browser';

/* App Root */
import { AppComponent }   from './app.component';
import { FormComponent }   from './form.component';

@NgModule({
  imports: [ BrowserModule ],
  
  declarations: [ AppComponent, FormComponent ],

  bootstrap:    [ AppComponent ]
})
export class AppModule { }
})
```

And yet, if you try to run this code it will not work, because we have used the directive **ngModel**, which according to RC5, needs to be imported in the 'App Module':

``` javascript
import { NgModule }       from '@angular/core';
import { BrowserModule }  from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';

/* App Root */
import { AppComponent }   from './app.component';
import { FormComponent }   from './form.component';

@NgModule({
  imports: [ BrowserModule, FormsModule ],
  
  declarations: [ AppComponent, FormComponent ],

  bootstrap:    [ AppComponent ]
})
export class AppModule { }
```

You can find [live example](http://plnkr.co/edit/iGwSjPdcO5hm0hI1e7wD?p=preview) here.

Hope you enjoyed this article.

Stay tuned and follow me on [twitter](https://twitter.com/lili_vs) :)