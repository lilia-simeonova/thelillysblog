---
layout: post
title:  "Beginner's guide to MEAN Stack - MongoDB, Express.js, Angular 2 and Node.js"
date:   2016-10-31 09:00:13
categories: Angular2
---

In this post we will create a simple registration form which store users in database and displays them.

<img src="{{ site.baseurl }}/images/mean.JPG">

# What is MEAN Stack?

It's a mixture of JavaScript based technologies which allow you to build single page applications. It gives you all the frameworks you need for both frontend and backend.


# Set up the environments

First we need to make sure that we have everything installed, so don't worry if you are not sure if those things are working at the moment, just follow the steps:

**Node.js**

One of the fisrt things to do, if you are playing for the first time with javascript is to install [Node.js](https://nodejs.org/en/download/).

**MondoDB and Mongoose**

It is pretty straight forward to run MongoDB, you simply need to to [download it](https://www.mongodb.com/download-center#community).


I chose to use Mongoose - MongoDB library, which provides **object modeling for Node.js**.

[Guide for installing Mongoose](http://mongoosejs.com/docs/index.html).


**Install Express.js**

Next, we need to install [Express.js](https://expressjs.com/en/starter/installing.html).

**Angular 2**

In my previous [blog post](http://thelillysblog.com/2016/10/08/registration-form-with-angular-seed/) there is a description how to use Angular with Angular-seed.

# Bring all of it together

Now we need to focus on how to make all of these technologies speak to each other. 

First we can create a simple database...

## MongoDB

In order to work with Mongo we need to start the server first. 

Open your terminal and go to the folder where you installed MongoDB, for me this is in *C:\Program Files\MongoDB\Server\3.2\bin.*

Once you are there type **./mongod.exe** in the console and if everything is going well you will see a message similar to:

 *"connection accepted from 127.0.0.1:54689 #2 <1 connection now open>"*

Afterwords we need to create a file, called db.js, where we will create our Scheme of users and will open the connection to the database.

```javascript
    var mongoose = require('mongoose');  
    var User = new mongoose.Schema({
    		  name: { type: String }
    		, age: Number
    		, email: String
    	});

   mongoose.model('User', User);  
   mongoose.connect('mongodb://localhost/'); 

   console.log('we are connected');
```

So far, so good, but how can we test it?

Here it comes the Express.js and Mongoose

## Express.js

After we successfully installed it, now it is time to use it.

In order to understand how this works we will create the fronend with our Express.js. 

[Skip directly on Angular 2 integration with Express.js](#angular2)

* Create index.html file 

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>MY APP</title>
</head>
<body>
  <h1>First Express page.</h1>
  <h2>Register user</h2>
  <form action="/createUsers" method="POST">
  	<input type="text" placeholder="name" name="name"><br><br>
  	<input type="number" placeholder="age" name="age"><br><br>
  	<input type="email" placeholder="email" name="email"><br><br>
  	<button type="submit">Submit</button>
  </form>
  <br>
  <form action="/seeResults" method="GET">
  	<button type="submit">See result</button>
  </form>
</body>
</html>
```
Now, we are sending our form to "create-users" page, but how to handle the data and save it our database?

* Create app.js file

This is our first express.js app.

With the first few lines we start a server and with *app.listen* function we listen on port 3000 for connections.

If you want to try it, you need to run the server, so open your console, go to the folder where you installed express and run **'node app.js'**. 

You will see in the console: 
*"Example app listening on port 3000!".*

```javascript
var express = require('express');

const bodyParser= require('body-parser')
var app = express();
var db = require('./db');

app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
  console.log(req.body);
})


app.listen(3000, function () {
  console.log('Example app listening on port 3000!');
});

```
After we have a running Express.js, it is time to connect it to our database.

In order to modify our database we should create another file to handle that: user.js:

```javascript
require('mongoose').model("User");

var mongoose = require( 'mongoose' );
var User     = mongoose.model( 'User' );

```
Here we declare that we are going to use the mongoose library with the User Scheme...

After that when the function createUsers is called, we will get the data from the form and will create a brand new user, which we will save in our Schema.

Additionaly, when function seeResults is called, we will display in the console every single user we have stored.

Also we will have a delete user function.

```javascript

require('mongoose').model("User");

var mongoose = require( 'mongoose' );
var User     = mongoose.model( 'User' );

module.exports = {
  createUsers: function(req, res) {
      var person = req.body;  
      new User({ name: person.name, age: person.age, email: person.email }).save();
      console.log('user saved');
      res.end('ok');
  },
  seeResults: function ( req, res, next ){
      User.find({}, function (err, docs) {
        for (var i = 0; i < docs.length; i++) {
          console.log('user:', docs[i].name);
        }
        res.end(JSON.stringify(docs));
      });
  },
  delete: function( req, res, next) {
    console.log(req.params.id);
    User.find({ _id: req.params.id}, function(err) {
      if(err) {
        req.status(504);
        console.log(err);
      }
    }).remove(function (err) {
          console.log(err);
          if (!err) {
            res.end('ok');            
          } else {
            res.end('error');
          }
      });
    console.log('we have the request');
  }
}

```
Back to our app.js file... as you remember we created a form in our *index.html* with two requests, so now it is time to declare what to do with them.

In order to modify our json files, we need to get the body-parser library.

With "app.post( '/createUsers', user.createUsers )" we are sending the user on "/createUsers" page where the functon createUsers from "users.js" file will be executed. 

The same goes for the seeResults function and delete function (with parameter), the only difference is that we will a get request, because we only need to retrieve data.

```javascript
var express = require('express');

const bodyParser= require('body-parser');
var app = express();
var db = require('./db');
var user = require('./user');

app.use(bodyParser.urlencoded({extended: true}));
app.use(bodyParser.json());

app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
  console.log(req.body);
})
app.post( '/createUsers', user.createUsers );
app.get( '/seeResults', user.seeResults );
app.delete( '/delete/:id', user.delete );

app.listen(3000, function () {
  console.log('Example app listening on port 3000!');
});

```

Congrats! Our Express.js app is working with the MongoDB. **Remember that in order to use it, you need to run both the mongo and express servers.**

Now that we are already familiar with Express.js and MongoDB, we will include Anglular 2 as well. So you need to forgot about the index.html file which we created in our little Express.js app.

## Angular2

In order **not to create everything from scratch** we will work on top of the application we had created in this [post](http://thelillysblog.com/2016/10/08/registration-form-with-angular-seed/) based on the [Angular-seed](https://github.com/mgechev/angular-seed).

With our registration component we have only been displaying users in the frontend. Now we will send them to our database.

Firstly we should import **HttpModule** in our registration.module.ts file, so that our application could talk to the server.

Let's take a look at the registration.component file.

Few things we should do: include the ***Http service*** and the observable.

After that we will make our RegistrationComponent class **@Injectable** to be able to call it as a service in our application.

Finally in out onSubmit function we send the **http post** request.

```javascript
import { Component, Injectable } from '@angular/core';
import { Person } from './person';
import { FormsModule } from '@angular/forms';
import { Http, Response, Headers, RequestOptions } from '@angular/http';
import { Observable } from 'rxjs/Observable';

@Component({
  moduleId: module.id,
  selector: 'sd-registration',
  templateUrl: 'registration.component.html',
  styleUrls: ['registration.component.css']
})

@Injectable()
export class RegistrationComponent { 
 constructor(public http: Http) { }

 persons = new Array();
 counter = 0;
 submitted = false;
 person;
  onSubmit(value) { 
    this.person = new Person(value.name, value.email, value.age);
    var headers = new Headers();
    headers.append('Content-Type', 'application/json');
    if(value) {
      this.persons.push(this.person);
    }
    this.counter++;
    this.submitted = true;
    this.http.post("http://localhost:3000/createUsers", JSON.stringify(this.person), {headers: headers}).subscribe(err => console.log(err));
  }
}

```

Now we want to be able to see all of the users in out database.

To do that we will create brand new module called "users" in our Angular 2 app and we will make it look like this:

<img src="{{ site.baseurl }}/images/users.gif">

In the users.component file (similar to registration component) we should create a data() function, which will retrieve all the data from our database.

```javascript
import { Component, Injectable } from '@angular/core';
import { Person } from '../registration/person';
import { FormsModule } from '@angular/forms';
import { Http, Response, Headers, RequestOptions } from '@angular/http';
import { Observable } from 'rxjs/Observable';


@Component({
  moduleId: module.id,
  selector: 'sd-users',
  templateUrl: 'users.component.html',
  styleUrls: ['users.component.css']
})

@Injectable()
export class UsersComponent { 
  constructor(public http: Http) { }
  public persons: any;
  data() {
     this.http.get('http://localhost:3000/seeResults')
       .map((res: Response) => res.json())
       .subscribe((res: any) => {
         this.persons = res;
         console.log("result is:",res);
       });
  };
  delete(person) {
    console.log(this.persons);
    //we need to concatenate the person's id in order to delete it
    this.http.delete('http://localhost:3000/delete/' + person._id).subscribe((res: any) => {
      this.persons.splice(this.persons.indexOf(person), 1);
    });
  }
}
```
Now we are ready to create single page application with MEAN stack.

