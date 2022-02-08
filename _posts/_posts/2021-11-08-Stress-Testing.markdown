---
layout: post
title:  "Stress Testing"
date:   2021-11-08
categories: blog engineering-journal sdc
---

# Pushing the service to the limits

## The testing setup

### Minimum Requirements

The minimum requirements our service needs to reach:
- 100 reqests per second
- Response times under 50ms
- Less than 5% failure rate

### The Software

I decided to use an open-source load testing software called K6. Many of my peers are also using this for their testing so it will be easy to get advice if I need help. There is an NPM package available so installation was a breeze, and the [documentation](https://k6.io/docs/) on setting up tests is very clear.

For the testing I wrote a function to generate random product numbers in the last 10% of the dataset:

```
export default function () {
  let num = randInRange(1000, 10000);
  http.get(`http://127.0.0.1:1337/reviews/meta?product_id=91${num}`);
}
```

### The results

The initial 100 request per second tests for the /reviews and /reviews/meta routes are shown below:

![Reviews Stress Test 100]({{ BASE_PATH }}/resources/reviewsstresstest_100persec.png)

/reviews - 4.91ms - 0% failure

![Meta Stress Test 100]({{ BASE_PATH }}/resources/metastresstest_100persec.png)

/reviews/meta - 10.94ms - 3.46% failure

The first results are looking good. I think the failure rate for the /meta route is because some products do not have reviews or characteristics in the database. If this becomes a problem I will do more research.

Pushing the service at 1000 requests per second:

![Reviews Stress Test 1k]({{ BASE_PATH }}/resources/reviewsbreakingpointfound.png)

![Meta Stress Test 1k]({{ BASE_PATH }}/resources/metabreakingpoint.png)

I definitely found the breaking point for the /reviews route. It looks like we max out around 744 requests per second before the connection throws an error that there are too many files open. The time also jumps up to 15.31 seconds for successful requests with an error rate of *43.73%*... yikes! I'll need to look into what is causing this bottleneck if I want to improve this route.

The /meta route is looking much better, but we did run into the the same "too many files open" error. It looks like some requests are left hanging as well, likely the result of lack of data and/or unhandled errors. The average requests per second is 717.66 with successful requests taking an average of 32.34ms, and only a 3.39% failure rate! I'm happy with this result for now, especially compared to the /reviews route. Time to dive into research for optimizations!

