---
layout: post
title:  " Test Facebook Messanger Bot on Localhost with Ngrok and Localtunnel"
date:   2017-05-03 09:00:13
categories: Chat Bots
---

In this article we are going to cover couple of ways how to handle the development process of building Facebook Messanger Chatbots.

You will ask why is this even a thing that needs to be discussed? Well... running a simple server on locolhost won't do the trick here. 

Imagine that we want to build a chat bot, based on Node.js and integrated with Facebook messenger using API.ai. 

The first thing that you will notice when you try to set up your Facebook page (the messenger part) in order to make it work with your custom code is the Webhooks.

`Webhooks allows your app to receive notifications whenever there are updates to a chosen set of topics and their fields.
Webhooks update notifications are sent as POST requests to a callback URL that you supply. `

Here you can find more information about the [webhooks](https://developers.facebook.com/docs/graph-api/webhooks).

All of this means that in order to have your Facebook bot working you will have to provide a real URL. 

You can use plenty of providers to do that (both free and commercial), but it is really time consuming to build app directly on a server. 
For every small test you should deploy the whole application and this can take a while.

The solution here is to use an app which creates a secure public URL to a local webserver on our machine.

# Ngrok

1. Download ngrok - [https://ngrok.com/download](https://ngrok.com/download)

2. Run your application with the local server (In my case I'm using Express.js)

3. Open ngrok and type in the console 

`ngrok http 8080`

We can replace 8080 with whichever port we are listening to.

Then something like this will appear:

<img src="{{ site.baseurl }}/images/ngrok.png">

4. After that we need to copy the https URL and paste it in our Facebook Webhook page:

<img src="{{ site.baseurl }}/images/webhook-ngrok.png">

# Localtunnel

One alternative to ngrok is [https://localtunnel.github.io/](https://localtunnel.github.io/)

1. Instead of downloading an executable, here we are going to install npm module:

`npm install -g localtunnel`

2. Again, we need to run our local server

3. Run the command `lt --port 8080` in the console and an URL will appear

4. Copy that in the Facebook Webhook page (see the screenshot from Ngrok section)



