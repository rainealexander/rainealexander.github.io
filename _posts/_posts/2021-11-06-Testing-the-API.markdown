---
layout: post
title:  "Testing the API"
date:   2021-11-06
categories: blog engineering-journal sdc
---

# Curl and Postman

Now that my API routes and queries are finished, it's time to test their performance. First up is confirming their functionality by using the `curl` command with my service running on localhost. The `-v` argument specifies verbose output with the details of the request object.

![Server not started]({{ BASE_PATH }}/resources/curlerror.png)

Note: it helps if you start your server before running this command, otherwise the request goes nowhere.

![Starting the server]({{ BASE_PATH }}/resources/serverstarted.png)

![Curl command use]({{ BASE_PATH }}/resources/curlcommand.png)

Much better. Now we can see the expected response from the server. After confirming that each route sent a response, I moved on to Postman for more thorough testing. In Postman we can save specific requests and group them as an 'API' for easy reuse. The main metric I needed before continuing was the response time for the queries, especially the GET requests. You can see the results in the images below:

![Reviews query response time]({{ BASE_PATH }}/resources/reviewsquery_initial_time.png)

Reviews Query time: 31ms

![Reviews Meta response time]({{ BASE_PATH }}/resources/reviewsmeta_initial_time.png)

Reviews Meta Query time: 41ms

It's important to note that I'm testing records in the last 10% of the dataset to make sure going through the million+ items does not cause an excessive slowdown. I added indexes to the collections before testing and all of the times are under the expected 50ms bechmark. Now that I know the routes are working and have the initial times, I can move on to stress testing the service.
