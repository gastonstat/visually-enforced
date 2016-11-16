---
layout: post
title: "Failing to read tables in R"
date: 2014-04-20
category: how-to
tags: [file, read, data]
image: read_table_failure.png
---

Badly disappointed with R by finding out that ```read.table()``` cannot read a file with a very simple structure... that Python is able to read.

<!--more-->

![](/images/blog/read_table_failure.png)


## A very simple file structure

Imagine you have some data in a text file having a very simple structure like the following one:


{% highlight r %}
1::Leia::female::princess
2::Luke::male::jedi
3::R2D2::unknown::robot
4::Anakin::male::jedi
5::Amidala::female::queen
{% endhighlight %}


As you can tell, we only have 5 lines each one containing 4 fields: a number, a name, a gender, and a class. Notice how the fields are separated with 2 colons ```::```. Here's a question for you: **How would you read this data in R?**

<hr/>

## Some background

Before we start solving the previous question, let me tell you a bit about how I discovered this challenging problem.

It all started when I was reading the book **Python for Data Analysis** by [Wes McKinney](http://blog.wesmckinney.com/). Being an enthusiast *useR*, I naturally felt tempted to replicate in R some of the case studies described in Wes' book, in particular the introductory case studies described in chapter 2.

One of the python examples required reading data from a text file that uses double colons (```::```) as a separator. For illustrating purposes I will use the made-up toy dataset already shown above: 


{% highlight r %}
1::Leia::female::princess
2::Luke::male::jedi
3::R2D2::unknown::robot
4::Anakin::male::jedi
5::Amidala::female::queen
{% endhighlight %}


In Python we can use the **pandas** library to import that kind of data without any problems. We just need to use the function ```read_table()``` and specify the separator ```sep='::'```. But what about R?


### Heartbroken by read.table()

If we try reading the data using one of the standard ways (e.g. ```read.table()```) I'm afraid we will be terribly dissapointed:


{% highlight r %}
# R doesn't like the separator '::'
bad_separator = read.table(
header = FALSE, 
text = "
1::Leia::female::princess
2::Luke::male::jedi
3::R2D2::unknown::robot
4::Anakin::male::jedi
5::Amidala::female::queen
",
col.names = c("num", "name", "gender", "class"),
sep = "::")
{% endhighlight %}



If you prefer, you can try reading the file ```fakedata.dat```, which has exactly the same content as the previous example, like so: 


{% highlight r %}
read.table(file = "http://gastonsanchez.com/data/fakedata.dat",
           header = FALSE, 
           col.names = c("num", "name", "gender", "class"),
           sep = "::")
{% endhighlight %}



In both cases, you should get a heartbreaking error like this one:

```
"Error in scan(file, what = "", sep = sep, quote = quote, nlines = 1:
  invalid 'sep' value: must be one byte"
```

### One byte separtors

If we had just one colon ```:``` as a separator, then R would not complain whatsoever:


{% highlight r %}
# R is ok with the separator ":"
good_separator = read.table(
header = FALSE, 
text = "
1:John:26:male
2:Dan:30:male
3:Rori:31:female
4:Tracy:40:female
5:Luke:30:male
",
col.names = c("num", "name", "age", "gender"),
sep = ":")

# look at the data
good_separator
{% endhighlight %}



{% highlight text %}
##   num  name age gender
## 1   1  John  26   male
## 2   2   Dan  30   male
## 3   3  Rori  31 female
## 4   4 Tracy  40 female
## 5   5  Luke  30   male
{% endhighlight %}


But the problem is that we do have two colons ```::```, and R accepts only one byte separators. Of course, we could use ```readLines()``` to read the data as a character vector, do some transformations to get rid of the colons, and then shape the data into a data frame. In some cases this option could be acceptable. But with the type of data like the one described in Wes's book, using ```readLines()``` was not an elegant option for me.

<hr/>

## My solution

The solution I found was to do the job outside of R. Yes, I know this is kind of dissapointing but it's part of the data analysis game. Sometimes you just have go outside R to get the job done.


### Using a text editor

One solution is to use a text editor (e.g. *vim*, *emacs*, *text wrangler*, *notepad ++*, *sublime text*, etc). You just need to use the find-replace functionality to substitute all double colons into commas:

![](/images/blog/replace_colons_by_comma.png)


### Using **sed**

In my case I work on a mac so I decided to use **sed** ([stream editor](http://en.wikipedia.org/wiki/Sed)). The idea is the same as if you were using a text editor: to substitute all the double colons by a comma. 

Here's how: open the **terminal**, go to the directory where the data file is, and simply run the following command:

```
sed 's/::/,/g' fakedata.dat > newfakedata.txt
```

Alternatively, you can use the ```system()``` function within R to pass it the same command:

{% highlight r %}
# 'sed' command from R
system("sed 's/::/,/g' fakedata.dat > newfakedata.txt")
{% endhighlight %}


What we're doing is substituting all the ```::``` by ```,``` in the file ```fakedata.dat``` and then saving the results in a new file ```newfakedata.dat```. If we open the new file we should be able to see nice comma-separated fields:


{% highlight r %}
1,Leia,female,princess
2,Luke,male,jedi
3,R2D2,unknown,robot
4,Anakin,male,jedi
5,Amidala,female,queen
{% endhighlight %}


which is perfectly readable in R with ```read.table()``` and/or ```read.csv()```.

