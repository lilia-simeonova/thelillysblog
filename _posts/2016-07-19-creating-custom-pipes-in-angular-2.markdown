---
layout: post
title:  "Creating Custom pipes in Angular 2"
date:   2016-07-19 09:00:13
categories: Angular2
---

<img src="{{ site.baseurl }}/images/pipe.png" height="200px">


## In this post we will discuss:

* What is Angular 2 pipe
* How to use it
* How to make custom pipes

## Pipes

In my last post we were discussing how to apply some styles to Angular 2 variable. Now we will take a look at how to format such variable in a different way.

You can think of pipes as some sort of filters or data formatters, which can apply styles to DOM elements, by binding to variables with Angular 2.

In Angular 2 we have pre-defined pipes, which allow us to make string uppercase or lowercase, to format data string and so much more.

## Usage
{% raw %}
As you can see in the code snippet, the only thing that we need to do, is to declare we want to use pipe with this symbol  <code>&#124;</code> and to specify which pipe from the core you want to use.


```javascript
import { Component } from '@angular/core';
@Component({
	selector: 'my-pipe',
	template: '<span> {{ myString  uppercase }} </span>',
})
export class PipeComponent{
	myString: string = 'test';
}
```


The key part here is  {{ myString  uppercase  }} where you are saying something like "I want to apply the "uppercase" filter to myString".

In the Angular 2 core, you can find others pre-defined pipes, such as LowerCasePipe, PercentPipe, ReplacePipe, SlicePipe, DatePipe and more (link to the docs).

It is worth it to pay special attention to the pipe DatePipe. If we set our variable myDate, as follows:

myDate: number = Date.now();
The output of {{ myDate }} would be 1468917543306, which is not very readable.

However if we apply the date pipe:

{{ myDate <code>&#124;</code> date}}

The output would be:  ‎Jul‎ ‎19‎, ‎2016.

We can also apply additional formatting:

{{ myDate <code>&#124;</code> date : yMMMMd}}

will display the full month - July instead of only Jul (December instead of Dec)

## Custom Pipes

In this section we will create our personal pipe, which will apply filter to array of numbers.

So let's create a file called pipes.component.ts

~~~js
import { Component } from '@angular/core';
import { FilterArray } from './custom.pipe';

@Component({
	moduleId: module.id,
	selector: 'my-pipe',
	templateUrl: 'pipes.component.html',
	styleUrls: ['pipes.component.css'],
	pipes: [FilterArray]
})
export class PipeComponent{
 	value: number[] = [1,2,3,4,5,6,7,8,9,10];
}

~~~

We will need to import our custom pipe and in the @Component decorator we need to state that we will use it.

The file custom.pipe.js would look like this:

~~~ js
import { Component, Pipe, PipeTransform } from '@angular/core';

@Pipe({name: 'filterArray'})
export class FilterArray implements PipeTransform {
  transform(array: number[], parameter: number): number[] {
   const newArray: number[] = [];
   if(isNaN(parameter)){
   	parameter = 10; // set up our default parameter to be 10 if we don't specify it.
   }
    for(let i = 0; i &lt; array.length; i++){
if(array[i] &gt; parameter){
    		newArray.push(array[i]);
    	}
    }
   return newArray;
  }
}
~~~
Here the new part is that we need to import the Pipe decorator and the PipeTransform interface from the core.

With @Pipe({name: 'filterArray'}) we specify how our pipe will be named.

Our class FilterArray will implement a specific version of the transform function, so the following code:

{{ value <code>&#124;</code> filterArray : 6}}
will display only the digits greater that 6.

So our initial array [1,2,3,4,5,6,7,8,9,10] will display as [7,8,9,10].



Feel free to comment or share and stay tuned for more Angular 2 posts.


{% endraw %}

If you like this, follow me on [twitter](https://twitter.com/lili_vs)!