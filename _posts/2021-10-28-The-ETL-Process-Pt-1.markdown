---
layout: post
title:  "The ETL Process, Pt 1"
date:   2021-10-28
categories: blog engineering-journal sdc
---

# Obstacles and Solutions

## The MongoDB Learning Curve

Having used MongoDB previously, I had a preconception that it wouldn't be too challenging for this process. I soon found out that there is a big difference between using Mongoose with a single schema and attempting to import data from four sources and combine them together.

### Research

While looking into options for the ETL[^1] process, I immediately discarded pre-made packages as on option. Throughout the Hack Reactor program it has been drilled into us that we should avoid "things that do the thing." While frustrating at first, I have come to appreciate the value of learning things the hard way first.

The dataset was provided in the form of CSV files and observing the seperation and field names, it was clear that it originated from a relational database. I found that MongoDB had a command line method to import csv files directly, but I decided to try creating my own method to transform the data with Node and Mongoose. This lead to me going down a rabbit hole of research that ended in creating a custom line-by-line csv reader (a story for another time).

---

[^1]: Extract, Transform, Load - a process for integrating data into a database