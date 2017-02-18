---
layout: post
title:  "Animated Shopping List with Angular"
date:   2017-02-19 09:00:13
categories: Angular2
---

In this blog post we will use some animations to make our shopping list looks better!

<img src="{{ site.baseurl }}/images/shopping.gif">

# Creating the list itself

Before adding animations to our code, let's first create simple form where we can add items for our list.

Assuming that you are already aware of the basic Angular structure, we will implement one component called ListComponent, having class with two functions - one to add items
in the list and one for removing items.

```javascript
import { Component } from '@angular/core';

@Component({
  moduleId: module.id,
  selector: 'sd-list',
  template: 'list.component.html',
  styleUrls: ['list.component.css']
})
export class ListComponent {

  newItem: string = '';
  items: any[] = [];

  addItem(): boolean {
    if(this.newItem) {
      this.items.push(this.newItem);
    }
    this.newItem = '';
    return false;
  }
  removeItem(value): boolean {
    var index = this.items.indexOf(value);
    this.items.splice(index, 1);
    return false;
  }

}
```
For now there is nothing special in our html template as well:

```html
<h2>Shopping list:</h2>
<form (submit)="addItem()">
  <input [(ngModel)]="newItem" autocomplete="off" name="newItem" placeholder="Add products">
  <button type="submit">Add</button>
</form>

<ul>
  <li *ngFor="let item of items" (click)="removeItem(item)">{{item}}</li>
</ul>

```

At the moment our list looks like this:

<img src="{{ site.baseurl }}/images/no-animations.gif">

Let's make it looks better!

# Animations in Angular

We will take few moments to take a look at the most important things we need to know while working with Angular Animations.

We have already imported the `Component`, now we should also import some animation-specific functions:

```javascript
import { Component, trigger, state, style, transition, animate } from '@angular/core';
```

## Triger function

`triger` is Angular animation-specific function, with main purpose to activate some changes within the elements when their state changes.

We need to implement it in our Component, right under the Animation section and also bind it to our html code.

Our component would look like this:

```javascript
@Component({
  moduleId: module.id,
  selector: 'sd-list',
  template: 'list.component.html',
  styleUrls: ['list.component.css'],
  animations: [
      trigger('smoothchange', [
          .........   
      ])
    ]
})
```

Our html code:

```html
<ul>
  <li [@smoothchange]="item.state" *ngFor="let item of items" (click)="removeItem(item)">{{item}}</li>
</ul>
```

## State function

The `state` function helps us define what is the current state of our element. 

If we don't specifically declare what would be the current state of the element,  the * (default state)  is triggered. 

The `void` state is reserved word which allows Angular to understand when the element is not part of the application anymore.

We can also create our own states - they are simply strings.

After we decide what are going to be the states of our element, we should define them within the `trigger` function.

The `state` function can have two arguments, the first is the name of the state and the second is the styles which the element should have while being it this state.

```javascript
@Component({
  moduleId: module.id,
  selector: 'sd-list',
  template: 'list.component.html',
  styleUrls: ['list.component.css'],
  animations: [
      trigger('smoothchange', [
        state('void', style({transform: 'translateX(0)'}))
      ])
    ]
})
```

## Transition function

The `transition` function defines how the elements would change while going from one state through the other.

## Animate function

The `animate` function declare for how long the required style would be displayed.

## Shopping list 

In order to create the flying in and flying out effect of our shopping items we can use the ccs transform function:

```javascript
animations: [
      trigger('smoothchange', [
        state('void', style({transform: 'translateX(0)'})),
        transition('void => *', [
          style({transform: 'translateX(-100%)'}),
          animate(300)
        ]),
        transition('* => void', [
        animate(300, style({transform: 'translateX(100%)'}))
        ])
      ])
    ]
```

There are a lot of fun things we can do with Animations, so stay tuned for more articles!