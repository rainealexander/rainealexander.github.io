---
layout: post
title:  "Optimization Research"
date:   2021-11-09
categories: blog engineering-journal sdc
---

# A Need for Speed

### Low hanging fruit

My first step in looking for optimizations was to double check the indexing on my MongoDB collections. I installed MongoDB Compass while working on queries for the API routes, and this software allows you to easily view indexes and their usage.

![Index information]({{ BASE_PATH }}/resources/reviews_indexes.png)

![Index info 2]({{ BASE_PATH  }}/resources/reviewsphotos_indexes.png)

As shown in the examples above, the indexes I added are being utilized in my queries, so no easy optimizations here.

The next piece I looked at was the connection to the database. There are many options that can be passed from Mongoose to our MongoDB server. I started by altering the `maxPoolSize` from 1000 to 2000 to see if there was any noticible difference.
```
mongoose.connect('mongodb://localhost:27017/reviews_service', {
  useNewUrlParser: true,
  maxPoolSize: 2000
});
```

![Pool size updated]({{ BASE_URL }}/resources/poolsizeupdate.png)

No noticeable change there. Out of curiosity I tried setting the pool size to 100, and surprisingly got almost exactly the same numbers. Next, looking into the error when requests start failing:

`error="Get \"http://127.0.0.1:1337/reviews?product_id=912580\": dial tcp 127.0.0.1:1337: socket: too many open files"`

It seems that this error is thrown by the OS when there are too many "file descriptors" open in linux. There is a command to check the limits set in the operating system: `ulimit -n`

![ulimit output]({{ BASE_PATH }}/resources/ulimitoutput.png)

After updating the limit x2 -> 2048:

![ulimit x2]({{ BASE_PATH }}/resources/ulimitdoubled.png)

limit x4 -> 4096:

![ulimit x4]({{ BASE_PATH }}/resources/ulimitx4.png)

There is some improvement after increading the file limit, but not significant. I happened to switch back to the server terminal while running this last stress test and saw something that set alarm bells going off... *console logs*. Turns out there were some logs i had missed removing, and what do you know... they were slowing things down *a lot*:

![console logs removed]({{ BASE_PATH }}/resources/consolelogsremoved.png)

After these optimizations and fixes I was able to push all of my routes to my personal goal of 1337 requests!!


### /reviews

![console logs removed]({{ BASE_PATH }}/resources/1337persec.png)

### /reviews/meta

![console logs removed]({{ BASE_PATH }}/resources/1337persecmeta.png)

### /reviews/*/helpful

![console logs removed]({{ BASE_PATH }}/resources/1337persechelpful.png)

### /reviews/*/report

![console logs removed]({{ BASE_PATH }}/resources/1337persecreport.png)