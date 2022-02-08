---
layout: post
title:  "Initial Deployment Tests"
date:   2021-11-16
categories: blog engineering-journal sdc
---

# Finding The Baseline

## Tests And Results

### K6 Local to Server

My first results are some quick tests run using K6 on my local machine to send http requests to the deployed service.

/reviews

![Deployment 200 req per second]({{ BASE_URL }}/resources/deployed200persec_reviews.png)

/reviews/meta

![Deployment 200 req per second]({{ BASE_URL }}/resources/deployed200persec_meta.png)

Both routes responded in about 50ms at 200 requests per second.

### Correcting An Error

After looking over the data, I realized I was providing product IDs that were outside of the range of data. These were quickly returning an empty result, and throwing off my times. With that adjusted, my times for 200 requests per second looked like this:

![Deployment 200 req per second]({{ BASE_URL }}/resources/deployed_200_reviews_redo.png)

/reviews is averaging just under 60ms with 0% errors

![Deployment 200 req per second]({{ BASE_URL }}/resources/deployed_200_meta_redo.png)

/reviews/meta is averaging about 86ms with 0% error

## Service Level Agreement

For our deployed service, the requirements are as follows:

- Less than 2000ms response under load
- Load of at least 100 requests per second
- Under 1% error rate

Now moving on to a web-based stress test tool called Loader.io, I will check the results of the service level tests completely 'in the cloud'.

/reviews at 200 requests per second - average 8ms - 0% error rate

![reviews baseline 200 per sec]({{ BASE_URL }}/resources/baseline_loaderio_200_reviews.png)


/reviews/meta at 200 requests per second - average 24ms - 0% error rate

![reviews baseline 200 per sec]({{ BASE_URL }}/resources/baseline_loaderio_200_meta.png)

---

/reviews at 400 requests per second - average 1481ms - 0% error rate

Much longer response, the longest going over the max of 2000ms. I think I'm up against the limits of a single t2.micro instance here.

![reviews baseline 400 per sec]({{ BASE_URL }}/resources/baseline_loaderio_400_reviews.png)

/reviews/meta at 400 requests per second - average 2655ms - 0% error rate (many less responses back)

This route definitely hit a wall. None of the responses come back within the allotted time, and there are many responses that have not come back at the end of the 60 second test.

![meta baseline 400 per sec]({{ BASE_URL }}/resources/baseline_loaderio_400_meta.png)

---

I took the clients per second down to 300 to see how the /meta route reacts:

![meta test 300 per sec]({{ BASE_URL }}/resources/baseline_loaderio_300_meta_tooslow.png)

The average is just under 2 seconds, but the longest is almost twice that. How about 250 per second?

![meta test 250 per sec]({{ BASE_URL }}/resources/baseline_loaderio_250_meta_strange.png)

Wow that is a big difference. Too big... I ran the tests again at 300 and 250 clients per second and got more reasonable results at 250 the second time:

![meta test 250 again]({{ BASE_URL }}/resources/baseline_loaderio_250_meta_again.png)

Since the /meta route seems to be the slowest, I'll start by digging there for my first optimizations on my deployed service.

