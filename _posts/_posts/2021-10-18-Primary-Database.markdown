---
layout: post
title:  "Primary Database"
date:   2021-10-18
categories: blog engineering-journal sdc
---

# Choosing a Primary Database

## Technical Justifications

### Debating on SQL vs. NoSQL

A large factor in deciding what database to use was the complexity of the data and API requests. For the Reviews service there are two GET routes for Reviews and Metadata, a POST route for new Reviews, and two PUT routes to mark Reviews as Helpful or Report a review. The data for Reviews is relatively simple, with only the Photos and Characteristics having multiple items. Considering these factors, I have decided to use MongoDB as my primary database.

With little cross-review data needed, a relational database seems like overkill. Any calculations such as scoring on product characteristics can be performed easily on a per-request basis as this only requires averaging a sum of numbers. The next step will be to explore the actual dataset and see if my planning holds up to the reality!
