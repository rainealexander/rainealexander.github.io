---
layout: post
title:  "The ETL Process, Pt 2"
date:   2021-10-29
categories: blog engineering-journal sdc
---

# Research Continued

After creating a csv reader, I realized that loading multiple files and combining their contents was a bit beyond my current skills. I turned back to MongoDB's method of importing files and began researching how it was done. My main concern was the Transform piece of the ETL process, as the CSV files only had field names and data.

I discovered that MongoDB has the option to read data types when importing files. These data types can even be added to the headers on a csv file in the form of `header.datatype()`. The types available in the import process did not exactly match up with what I had chosen in my Mongoose schemas, so some of the data would need to be transformed after import or on a per-query basis. After gaining confidence in the datatypes, I decided to import the csv files separately and attept to combine the data later if needed.

Updating the csv files to include the datatypes took a bit of extra research. Opening the files in a text editor prompted an error message that the file was too large to load entirely - apparently generic editors aren't built to handle 1 million plus lines of text. Luckily I discovered the `sed` command that allows you to find and replace a single line of text in a file.

![sed command]({{ BASE_PATH }}/resources/sedcommand.png)

Now that each of the csv files was updated with the datatypes, all that was left was to import the data into MongoDB with the command line (example below):

![data import]({{ BASE_PATH }}/resources/importcommand.png)
