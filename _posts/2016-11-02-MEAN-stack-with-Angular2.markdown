---
layout: post
title:  "Beginner's guide to MEAN Stack - MongoDB, Express.js, Angular 2 and Node.js"
date:   2016-11-02 12:00:13
categories: Angular2
---

In this post we will create a simple registration form, which store users in database and displays them.

<img src="{{ site.baseurl }}/images/mean.JPG">

# What is MEAN Stack?

It's a mixture of JavaScript based technologies which allow you to build single-page applications. It gives you all the tools you need for both frontend and backend.

# Set up the environments

First we need to make sure that we have everything installed, so don't worry if you are not sure if those things are working at the moment, just follow the steps:

**Node.js**

One of the fisrt things to do, if you are playing for the first time with JavaScript, is to install [Node.js](https://nodejs.org/en/download/).

**MondoDB and Mongoose**

It is pretty straightforward to run MongoDB, you simply need to [download it](https://www.mongodb.com/download-center#community).


I chose to use Mongoose; library for MongoDB, which provides **object modeling for Node.js**.

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

Open your terminal and go to the folder where you installed MongoDB, for me this is in `C:\Program Files\MongoDB\Server\3.2\bin.`

Once you are there type `./mongod.exe` in the console and if everything is going well you will see a message similar to:

 *"connection accepted from 127.0.0.1:54689 #2 <1 connection now open>"*

Afterwards we need to create a file, called db.js, where we will create our Scheme of users and will open the connection to the database.

```javascript
var mongoose = require('mongoose');  
var User = new mongoose.Schema({
	name: { type: String },
	age: Number,
  email: String
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

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>MY APP</title>
</head>
<body>
  <h1>First Express page.</h1>
  <h2>Register user</h2>
  <form action="/users" method="POST">
  	<input type="text" placeholder="name" name="name"><br><br>
  	<input type="number" placeholder="age" name="age"><br><br>
  	<input type="email" placeholder="email" name="email"><br><br>
  	<button type="submit">Submit</button>
  </form>
  <br>
  <form action="/users" method="GET">
  	<button type="submit">See result</button>
  </form>
</body>
</html>
```
Now, we are sending our form to "createUsers" page, but how to handle the data and save it our database?

* Create app.js file

Now lets build our first express.js app. Create a file called **app.js** with the following content:

```javascript
var express = require('express');

const bodyParser= require('body-parser');
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

Take a look at the file above. With the first few lines we create a server and with *app.listen* function we listen on port 3000 for connections.

If you want to try it, you need to run the server, so open your console, go to the folder where you installed express and run `node app.js`. 

You will see in the console: 
*"Example app listening on port 3000!".*

After we have a running Express.js, it is time to connect it to our database.

Now let's create our mongoose models which are going to communicate with the database. Create a file called **user.js**:

```javascript
require('mongoose').model('User');

var mongoose = require('mongoose');
var User = mongoose.model('User');
```

Here we declare that we are going to use the mongoose library with the **User** model...

Alright, take a look at the snippet below. When the function **createUsers** is called, we will get the data from the form and will create a brand new user, which we will save in our database.

Additionaly, when function `seeResults` is called, we will display in the console every single user we have stored.

Also we will have a delete user function.

```javascript
require('mongoose').model('User');

var mongoose = require('mongoose');
var User = mongoose.model('User');

module.exports = {
  createUsers: function (req, res) {
    var person = req.body;
    new User({ name: person.name, age: person.age, email: person.email })
      .save(function (err) {
        if (err) {
          res.status(504);
          res.end(err);
        } else {
          console.log('user saved');
          res.end();
        }
      });
  },
  seeResults: function (req, res, next) {
    User.find({}, function (err, docs) {
      if (err) {
        res.status(504);
        res.end(err);
      } else {
        for (var i = 0; i < docs.length; i++) {
         console.log('user:', docs[i].name);
        }
        res.end(JSON.stringify(docs));
      }
    });
  },
  delete: function( req, res, next) {
    console.log(req.params.id);
    User.find({ _id: req.params.id}, function(err) {
      if(err) {
        req.status(504);
        req.end();
        console.log(err);
      }
    }).remove(function (err) {
      console.log(err);
      if (err) {
        res.end(err);            
      } else {
        res.end();
      }
    });
  }
}
```

Back to our **app.js** file...as you remember we created a form in our **index.html** with two requests, so now it is time to define what to do with them.

In order to parse the body of the HTTP requests as JSON, we need to use the body-parser library.

With `app.post('/createUsers', user.createUsers)` we are accepting POST requests on "/createUsers" URI where the functon `user.createUsers` from "users.js" file will be executed. 

The same goes for the `seeResults` function which will be invoked once the user sends a GET request to "/seeResults". On the other hand, `user.delete` will be called on HTTP DELETE request to `/delete/:id`. Notice that the `/delete` path ends with `:id`. This means that it accepts a single parameter - the ID of the user.

```javascript
var express = require('express');

const bodyParser= require('body-parser');
var app = express();
var db = require('./db');
var user = require('./user');

app.use(bodyParser.urlencoded({extended: true}));
app.use(bodyParser.json());

app.get('/', function (req, res) {
  res.sendFile(__dirname + '/index.html');
});

app.post('/users', user.createUsers);
app.get('/users', user.seeResults);
app.delete('/users/:id', user.delete);

app.listen(3000, function () {
  console.log('Example app listening on port 3000!');
});

```

Congrats! Our Express.js app is working with the MongoDB. **Remember that in order to use it, you need to run both the mongo and express servers.**

Now that we are already familiar with Express.js and MongoDB, we will include Anglular 2 as well. So you need to forget about the `index.html` file, which we created in our little Express.js app.

## Angular2

In order **not to create everything from scratch** we will work on top of the application we had created in this [post](http://thelillysblog.com/2016/10/08/registration-form-with-angular-seed/) based on the [Angular Seed](https://github.com/mgechev/angular-seed).

With our registration component we have only been displaying users in the frontend. Now we will send them to our database.

Firstly we should import `HttpModule` in our `registration.module.ts` file, so that our application can talk to the express server.

Let's take a look at the **registration.component.ts** file.

The `RegistrationComponent` accepts a single argument of type `Http`.

We use the injected `Http` instance in the `onSubmit` function in order to send a **http post** request.

```javascript
import { Component, Injectable } from '@angular/core';
import { Person } from './person';
import { FormsModule } from '@angular/forms';
import { Http, Response, Headers, RequestOptions } from '@angular/http';

@Component({
  moduleId: module.id,
  selector: 'sd-registration',
  templateUrl: 'registration.component.html',
  styleUrls: ['registration.component.css']
})
export class RegistrationComponent { 
  constructor(public http: Http) { }

  persons = [];
  counter = 0;
  submitted = false;
  person;
  onSubmit(value) { 
    this.person = new Person(value.name, value.email, value.age);
    var headers = new Headers();
    headers.append('Content-Type', 'application/json');
    if (value) {
      this.persons.push(this.person);
    }
    this.counter++;
    this.submitted = true;
    this.http.post('http://localhost:3000/createUsers',
      JSON.stringify(this.person), { headers: headers })
      .subscribe(err => console.log(err));
  }
}
```

Now we want to be able to see all of the users from the database.

To do that we will create a brand new module called "users" in our Angular 2 app and we will make it look like this:

<img src="{{ site.baseurl }}/images/users.gif">

In the `users.component.ts` file (similar to registration component) we should create a `data()` method, which will retrieve all the data from our database.

```javascript
import { Component } from '@angular/core';
import { Person } from '../registration/person';
import { FormsModule } from '@angular/forms';
import { Http, Response, Headers, RequestOptions } from '@angular/http';

// We need it because of the `map` method we use below
import { Observable } from 'rxjs/Observable';


@Component({
  moduleId: module.id,
  selector: 'sd-users',
  templateUrl: 'users.component.html',
  styleUrls: ['users.component.css']
})
export class UsersComponent { 
  constructor(public http: Http) { }
  
  public persons: any;
  
  data() {
    this.http.get('http://localhost:3000/seeResults')
     .map((res: Response) => res.json())
     .subscribe((res: any) => {
       this.persons = res;
     });
  }

  delete(person) {
    this.http.delete('http://localhost:3000/delete/' + person._id).subscribe((res: any) => {
      this.persons.splice(this.persons.indexOf(person), 1);
    });
  }
}
```

Now we already have our single page application with MEAN stack!

If you like this, follow me on [twitter](https://twitter.com/lili_vs)!