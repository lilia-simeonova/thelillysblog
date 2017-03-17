---
layout: post
title:  "How to develop a chatbot in less than 200 lines of JavaScript"
date:   2017-03-16 09:00:13
categories: 
---


The main idea behind the chatbots is to save us time, money, do something for us or teach us something.

In the next couple of lines I will go through all the steps for creating a bot which can do some stuff for us.

# Designing GitHub bot

## What it's expected from the bot

<img src="{{ site.baseurl }}/images/tinder.gif" width="600" height="350">


Just kidding, we won't be designing tinder bot. 

We will develop a chatbot which performs different actions using the GitHub API. Its features will be:

* Search for repositories and users
* Create a new repo for us
* Follow user for us


## Main Modules

Each chatbot has three high-level modules:

* Accepting user input
* Processing and analyzing the input
* Performing an action

## Pick the right technology

Firstly, we should decide how to accept our input -  we can write our own messaging service from scratch or we can use existing one - facebook messenger, slack, twitter, etc.

The second (trickiest) part is making the computer understands what exactly the user wants/means by their input.
We can go in two main directions - implement custom logic for natural language processing or use a service which provides this for us. There are plenty of such services and all of them have their pros and cons.

Last, but not least we need to pick technology for implementing the back-end and the business logic of the service we are building.   

In this case I have decided to use the following technologies:

* Natural language process technology - [api.ai](https://api.ai) by Google.
* Messaging service - Slack
* Back-end - Node.js

## Integrate all the tools 

<img src="{{ site.baseurl }}/images/integrations.jpg">

In the beginning we should spend some time clicking through api.ai and slack websites in order to configure our integrations. We can do that in two steps:

1. Create our app in api.ai - [see here](https://docs.api.ai/docs/get-started)

2. Create api.ai/slack integration - [see here](https://docs.api.ai/docs/slack-integration)

Once we are done with the integrations, we need to go to the `Domains` tab in api.ai and enable the `Small Talk` domain, it is free, customizable and it allows our bot to have simple conversations with the users. It can answer to greetings and questions like “How are you?”, “Who are you?”, etc.

If everything is going as expected, we can now speak to our bot via Slack!

We have already decided which would be the different use cases for the bot, now is the time to train our bot to recognize them.

## Creating Entities

Entities represent concepts and serve as a powerful tool for extracting parameter values from natural language inputs. [Here](https://docs.api.ai/docs/concept-entities) you can read more about them.

There are couple of ways to import entities in the tool - you can do it manually or you can directly copy/paste json or csv file. 

<img src="{{ site.baseurl }}/images/entities.gif">

The more entities you add to your bot, the more clever it becomes. 

We are going to teach the bot to recognize specific entities for searching project, searching user, following user and creating new repo.

CSV content for entity `searchProject`

```
"repository","repository","repo"
"project","project"
```

CSV code for entity `searchUser`

```
"user","user"
"profile","profile"
"github","github"
```

CSV code for entity `createRepo`

```
"repository","repository"
"project","project"
"repo","repo"
```

CSV code for entity `followUser`

```
"follow","follow"
```

## Creating Intents

In api.ai we are using `intents` as a way to recognize different user stories. [Here](https://docs.api.ai/docs/concept-intents) you can find detailed explanation about them.

We can add intents manually or use the Beta Training Tool of api.ai to upload them.

In the current design of the bot, it makes sense to have 3 different intents:

* Perform a search
* Create new repo
* Follow user

*Note: We have created two search entities - one for searching user and one for searching repos. As we will see later, our API gives us two different functions for those actions that is why we needed to create a way of identifying which action should be invoked.*

For every intent we can provide a bunch of training examples.

The best way to train our bot is to use some real life data examples. In case we don’t have such, like in this particular case, we can come up with some queries, which we think the users would ask.

This is an example of `Make a search` intent:

<img src="{{ site.baseurl }}/images/intents.jpg">

As you can see, we provide example questions and mark the entities within them.

Similar thing needs to be done for all intents.

When we finish filling in the examples there, we should specify what is supposed to happen after the bot recognizes particular intent.

Right under the training examples of every intent there is a field called `Action`. By setting values to it, we can control what is going to happen in our custom code later on.

 *Note that at this point we still haven't wrote a single line of JavaScript!*

<img src="{{ site.baseurl }}/images/questions.gif">

## Integrate our own functionality within the bot

In order to make our life easier we are going to use this [repo](https://github.com/xVir/api-ai-slack-bot) which provides  Api.ai / Slack integration for Node.js out of the box. 

After we clone it locally, we need to provide the api.ai and slack access tokens. 

*While writing our custom code we should be careful and set proper timeouts in case our RESTful request hangs somewhere.*

Back to our project, opening the `src/index.js` file, we can jump to the `request.on` function and define two more variables - `action` and `parameters`, as described in the code snippet below. 

```javascript
// Some code here
//---------------------------------------------------------------------
// Need to assign api.ai and slack tokens to environment variables
//---------------------------------------------------------------------
const apiAiAccessToken = process.env.accesstoken;
const slackBotKey = process.env.slackkey;

//---------------------------------------------------------------------
// More code here
//---------------------------------------------------------------------
 request.on('response', (response) => {

   if (isDefined(response.result)) {
      let responseText = response.result.fulfillment.speech;
      let responseData = response.result.fulfillment.data;
      
      // The action variable would be the value that we defined under every intent.
      let action = response.result.action;

      // The parameters variable will show us the value of the entities.
      let parameters = response.result.parameters;
      
      if (action === 'search') {

        if (searchProject) {
          // some business logic goes here

        } else if (searchUser) {
          // some business logic goes here

        } else if(action === 'follow') {
          // some business logic goes here

        } else if(action === 'createRepo') {    
          // some business logic goes here

        }
    }
  // more code
  }
});      
```

## GitHub API

Our initial goal was to create a bot, which can make some github related actions on our behalf.

We are going to use the npm [github api](https://www.npmjs.com/package/github-api) module in order to achieve that.

 1) Install the npm package:

```
npm install github-api --save
```

 2) Import it in the beginning of the file `index.js` and authenticate.

```javascript
var GitHub = require('github-api');

var github = new GitHub({ token: "your-github-token"});
```

So now we can have a closer look at the different intents.

Let's start with the search intent.

One thing to mark here is that we can have two types of search - for users and for projects/repos.

That is why we have created two separate entities - `searchProject` and `searchUser`.

Here is a closer look at the `search` intent:

```javascript
if (action === 'search') {
  let searchProject = response.result.parameters['searchProject'];
  let searchUser = response.result.parameters['searchUser'];
  let text = response.result.parameters['text'][0];
  let search = github.search();
  if (searchProject) {

    // firstReady is function handling custom timeouts
    firstReady(
      search.forRepositories({ q: text, sort: 'stars', order: 'desc'}),
      timeoutAfter(TimeoutDuration)
    )
    .then(r => { 
      if (r.data) {
        console.log(r.data);
        responseText = 'Here are the first 3 result:' + '\n' + r.data[0]['full_name'] + '\n' + r.data[1]['full_name'] + '\n' + r.data[2]['full_name'];
        bot.reply(message, responseText);
      } else {
        responseText = 'Sorry, I can\'t find project related to ' + text + '. Please try one more time';
        bot.reply(message, responseText); 
      }
    })
    .catch(e => {
      console.log(e);
      console.log('Something went wrong');
      bot.reply(message, e)
    });
  } else if (searchUser) {
    firstReady(
      search.forUsers({ q: text}),
      timeoutAfter(TimeoutDuration)
    )
    .then(r => {
      let user = r.data[0]['html_url'];
      if (user) {
        let responseText = user;
        bot.reply(message, responseText);
      } else {
        let responseText = 'I can\'t find this user\'s profile, please try again';
        bot.reply(message, responseText);
      }         
    })       
  }
}
```
** Note that in this example we are not covering all of the corner cases - in production most likely you will need to write a lot more if-else statements. **

Basically the same is happening for the other two intents:

```javascript
else if (action === 'follow') {
  let result = response.result.parameters['text'];
  if (result) {
    let user = github.getUser(result);  
    firstReady(
      user.follow(),
      timeoutAfter(TimeoutDuration)
    )
    .then(r => { 
      if (r) {
        let responseText = 'You successfuly followed ' + result;
        bot.reply(message, responseText);
      } else {
        responseText = 'There was some problem. Please try again.';
        bot.reply(message, responseText);
      }  
    });
  }   
} else if (action === 'createRepo') {
  let result = response.result.parameters['text'];
  if (result) {
    let user = github.getUser();
    firstReady(
      user.createRepo({ name: result }),
      timeoutAfter(TimeoutDuration)
    )
    .then(r => {
      let responseText = "You successfuly created repo " + result;
      bot.reply(message, responseText);
    });
  }  
}
```

I started this project only for fun, but if you want to help me make this thing actually live you can do it in [GitHub](https://github.com/lilia-simeonova/github-slack-apiai-bot) or talk to me on [twitter](https://twitter.com/lili_vs).

<img src="{{ site.baseurl }}/images/github-gif.gif" width="600" height="350">

