---
layout: post
title:  "Encountering the Data"
date:   2021-10-19
categories: blog engineering-journal sdc
---

# Accessing the actual Dataset

## Does it fit the mold?

Now that we have created our schemas and the primary database has been chosen, access has been granted to our dataset. It was mildly shocking to find that there are over a million records for the reviews, but it is nice to have something that is a similar scale to the real world. The only other surprise was discovering that the review categories such as Size, Comfort, etc. do not have consistent IDs for each product. Luckily we are provided with a table to match the IDs to their category name.

![Characteristics schema](https://cors.io/?https://github.com/rainealexander/rainealexander.github.io/blob/master/resources/charschema.png)

My current schema should still work as there are no additional categories, only different IDs that map to them.

The final piece to review was the date field for reviews. The dataset contains a number that seems to relate to milliseconds in a JavaScript Date. A brief test in the browser console confirmed this to be true:

![Date Test](https://cors.io/?https://github.com/rainealexander/rainealexander.github.io/blob/master/resources/datetest.png)

Now that I understand the data I'll be working with, the next step is to install MongoDB and Mongoose, then research the ETL process!
