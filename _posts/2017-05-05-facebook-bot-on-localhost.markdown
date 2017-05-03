---
layout: post
title:  "Facebook Messanger Bot on Localhost with Ngrok and Localtunnel"
date:   2017-05-03 09:00:13
categories: Chat Bots
---

In this article we are going to discuss how to make our development process easier when it comes to creating Facebook Messanger Chatbots.

Imagine that we want to build a chat bot, based on Node.js and integrated with Facebook messenger using Api.ai. 

In order to set up the whole application (the messenger part with our custom code) we need to take care of the Webhooks.

Wait, what is that?

`Webhooks allows your app to receive notifications whenever there are updates to a chosen set of topics and their fields.
Webhooks update notifications are sent as POST requests to a callback URL that you supply. `

More about the Webhooks: [https://developers.facebook.com/docs/graph-api/webhooks](https://developers.facebook.com/docs/graph-api/webhooks).

All of this means that in order to have our Facebook bot working we will have to provide a real URL. 

We can use plenty of providers to do that (both free and commercial), but it is really time consuming to build an app directly on a server. 
For every small test we need to deploy the whole application and ...this can take a while.

<img src="{{ site.baseurl }}/images/waiting.gif">

We want to avoid waisting time, right?

The solution here is to use an app which creates a secure public URL to a local webserver on our machine.

Below we can take a look at some of the solutions that work for me:

# Ngrok

1. Download ngrok - [https://ngrok.com/download](https://ngrok.com/download)

2. Run your application with the local server (In my case I'm using Express.js)

3. Open ngrok and type in the console 

    `ngrok http 8080`

    We can replace 8080 with whichever port we are listening to.

    Then something like this will appear:

    <img src="{{ site.baseurl }}/images/ngrok.JPG">

4. After that we need to copy the https URL and paste it in our Facebook Webhook page:

    <img src="{{ site.baseurl }}/images/webhook-ngrok.JPG">

# Localtunnel

One alternative to ngrok is [https://localtunnel.github.io/](https://localtunnel.github.io/)

1. Instead of downloading an executable, here we are going to install npm module:

    `npm install -g localtunnel`

2. Again, we need to run our local server

3. Run the command `lt --port 8080` in the console and an URL will appear

4. Copy that in the Facebook Webhook page (see the screenshot from Ngrok section)


Voila, we are now able to test our bot locally. 