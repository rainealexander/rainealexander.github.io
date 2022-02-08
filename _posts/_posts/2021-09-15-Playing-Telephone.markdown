---
layout: post
title:  "Playing Telephone"
date:   2021-09-15
categories: blog engineering-journal
---

# Calling the API

## Challenge

Implement a successful API call starting from the Client through the Server to the API

## Actions

I started off attepting to use jQuery's ajax function to make a call to the server. This brought to light issues with importing the library and the client could not find the definition for "ajax". After some time troubleshooting, I decided to switch to using "axios" to make the HTTP requests. Once over the brief learning curve, I determined how to pass parameters with a GET request and read this information on the server side. From here I just had to make a call to our API with the GitHub Token passed as authorization.

## Results

After the decision to switch to axios, the request chain was reletively easy to implement. From React we use componentDidMount to create a get request on load, the server parsed the product ID and sent a get request to the product API, on success we recieved an object containing the full details of the requested product.
