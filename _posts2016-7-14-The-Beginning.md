---
layout: post
title: The Beginning
---

First blog post and one step closer to becoming a data scientist!

About to finish my second week at Metis located in SoMa SF. So far the experience has been awesome. We've learned a lot from our instructors like python, probability, regression models, basic machine learning, pandas, data visualization, web scraping, etc. (hard to believe it's only week 2!). And it's been fun getting to know the rest of the cohort exploring the local eats of SF.

I'm a bit new to the blogging world and data science, so I'll stick with some of what I've done the past week or so...

## New York Subway // Real World Data Munging // Python

Coming from primarily an SQL background and Google where data is stored nicely and itegrates flawlessly (usually) with other tools, working with real world data and Python is new and exciting. In my first week at Metis we worked with data from the New York Subway (MTA). If you're interested, MTA makes all their turnstile data publically available [here](http://web.mta.info/developers/turnstile.html).

Python has some great packages like Pandas for data munging, but for the sake of learning I decided to good old fashioned Python dictionaries. It's more tedious, but I'd recommend data munging at least once with dictionaries because (1) it's a good learning experience for working with data in Python, and (2) there will be times when the data is too large to simply use Pandas and you'll need to know how to work with the raw data line by line. So here are some of the steps that I took for data munging the MTA data...

### Step 1: Collect Data

It should go without saying, but data munging requires that you have data. The link I share earlier has all the MTA turnstile data. What's cool is that MTA already provides .txt files for each week that you can download. However, that can become tedious when you want to look for patterns in more than a handful of weeks. Luckily, there's a way to make this easier.

In Python, use a loop and wget to retrieve the .txt files. Write a function to read the contents of the files row by row and save them in a list. Use glob to get all the pathnames of the .txt files. Then call your function to create a list all the data (or whatever format is best for you to use).

Here's a sample:

```python
url_template = 'http://web.mta.info/developers/data/nyct/turnstile/turnstile_%s.txt'
for date in ['160702','160625']:
    url = url_template % date
    !wget {url}
    
def turnstile_read_csv(csv_file_name):
    turnstile_data = []
    with open(csv_file_name, 'r') as csv_file:
        mta_reader = csv.reader(csv_file)
        for row in mta_reader:
            # strip whitespace
            row[10] = row[10].strip()
            turnstile_data.append(row)
    
    # remove header
    turnstile_data.pop(0)
    
    return turnstile_data
    
data_files = glob.glob("turnstile_*.txt")
all_data = sum(map(turnstile_read_csv, data_files), [])
```

### Step 2: Data Munging

So this was the painful part because I was working with dictionaries and the data had strange formatting issues or anamolies. I created multiple dictionaries with different keys like turnstile and station or just the station that were associated with data, entry, and exit values.

It took me multiply iterations to format the data the way I wanted because I'd notice different issues. First, MTA stored the data with extra whitespace for the last value of every row. Second, the turnstile counts would reset every once in a while, which led to getting negative or unrealistically large counts. Simply taking the max and min per time frame I was interested in would work because the min value may have been from a reset and not the starting value for the week. What I did was a little hacky, but set the count to 0 for the day there was a reset, which I identified if the count was lower for the following day (MTA keeps a rolling count, so the values shouldn't decrease).

### Step 3: Simple Analysis

My project group identified the top stations by ridership and broke down the times into meal time buckets. We realized a discrepancy in when the turnstiles start recording entries, exits. They all record by four hour chunks, but may start at different hours. However, for the sake of this post I'll stick to some very basic things you can do once you've cleaned up your data.

#### Basic Plots and Trends ####

Weekday vs Weekend
![](/images/1_blog_1.png)

Here we can identify a simple trend by plotting total ridership for a week for a given turnstile. Visually we can identify the weekends by the dips in the plot.

Week to Week
![](/images/1_blog_2.png)

Here we can compare the total ridership per week for a given turnstile. In this way, one of the weeks seems to jump up on Saturday. Seeing this we can potentially dig in more and try to find out what may have caused the increased ridership.

## That's All Folks

Some takeaways:
* Dictionaries are fun, but stick with Pandas for most of your data munging.
* Python is awesome. Lot more potential here than simply using SQL.
* Get working code first, then iterate and clean up.
* Data visualization is powerful and shows trends that aren't as obvious with stats summaries.

That's all for now. Hope you enjoyed the post. Signing off.
