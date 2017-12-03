---
layout: post
title: Converting HTML files to PDF
date: 2017-11-10
categories: how-to
tags: [convert, html, wkhtmltopdf]
image: wkhtmltopdf.png
---

Using [wkhtmltopdf](https://wkhtmltopdf.org/) to convert HTML files
into PDF format.

<!--more-->

<img src="{{ site.baseurl }}/images/blog/wkhtmltopdf.png">

## Motivation

For one of the courses I'm teaching this semester 
([Stat 133: Concepts in Computing with Data](https://github.com/ucb-stat133/stat133-fall-2017)), I asked students to write a _blog post_.
To be more precise, I asked them to write a report, in the form of a blog post, about one or more of the central topics covered in the course such as: 

- Data Visualization
- Data Manipulation (reshaping, wrangling, formatting, tidying)
- Programming for data analysis
- Data Technologies
- Reporting Tools

The submission format of the posts was in HTML. To keep things simple I
asked them to use an R Markdown (`Rmd`) file, and _knit_ it as an HTML
document (the default knitting option), that they uploaded to a _BOX_ folder.
This means I ended up with about 270 HTML files that I wanted to share
with all the students (and with the rest of the world). The issue was that 
HTML files don't get rendered nicely in BOX or in GitHub.

So my problem became: How do I convert 270 HTML into PDF format ... in an 
efficient way? I knew I could manually open each file, and then save it as PDF.
But I didn't want to repeat a handful of steps 270 times!

Luckily, I found a command line tool called 
[wkhtmltopdf](https://wkhtmltopdf.org/), which is exactly what I needed.

__wkhtmltopdf__ is an open source command line tool to render HTML into PDF 
format using the _Qt WebKit_ rendering engine. To convert an HTML file
to PDF you simply run the `wkhtmltopdf`. Here's the example used in the 
homepage of _wkhtmltopdf_ to convert the Google logo and as a PDF:

{% highlight bash %}
wkhtmltopdf http://google.com google.pdf
{% endhighlight %}


-----


## Shell script

To convert all the files at once I wrote a shell script called
`convert2pdf.sh` (see code below). To write the script I considered
the following assumptions:

- all the HTML files are in a folder called `htmls`
- all HTML files have extension `.html`
- the converted PDFs will be stored in a folder called `pdfs`

Here's what the assumed file structure would look like:

{% highlight bash %}
mydir/
    convert2pdf.sh
    htmls/
        post01-deb-nolan.html
        post01-ani-adhikari.html
        post01-bin-yu.html
        post01-phil-stark.html
        post01-fernando-perez.html
    pdfs/
        ...
{% endhighlight %}



Here's the content of the `convert2pdf.sh` script:

{% highlight bash %}
#!/bin/sh

# names of files (without extension)
files=$(ls -1 htmls | sed -e 's/\.html$//')

# convert files
for file in $files
do
	echo "converting ${file}.html to ${file}.pdf"
	wkhtmltopdf --dpi 1000 htmls/${file}.html pdfs/${file}.pdf
done
{% endhighlight %}


### What's going on?

The first command involves creating a variable `files` that contains
the names of the HTML files (without the file extension). More specifically,
I'm using `ls` to list the contents of the `htmls` directory, and then
I pipe the output to a `sed` command. The `sed` command basically 
replaces the file extension with nothing (i.e. removes file extension).

The second part of the script consists of a `for` loop. At each iteration 
of the loop two commands are invoked: `echo` and `wkthmltopdf`.

The `echo` command is not that important, it's just an informative message 
that displays the name of the file that is being converted at that iteration.
In case things go wrong and the file conversion stops, you may want to 
know which file failed to be converted.

Then we have the `wkhtmltopdf` command that takes an input HTML files
and converts it into an output PDF file. As you can tell, this command
also uses the option `--dpi 1000` (dots-per-inch). I found that I needed
to use this option to avoid generating a PDF file with microscopic
content. You probably want to try differnte dpi values and see which one 
is more convenient for you.

One option to run the shell script is with the `sh` command:

{% highlight bash %}
sh convert2pdf.sh
{% endhighlight %}

Once the loop is completed, the file structure should now look like this:

{% highlight bash %}
mydir/
    convert2pdf.sh
    htmls/
        post01-deb-nolan.html
        post01-ani-adhikari.html
        post01-bin-yu.html
        post01-phil-stark.html
        post01-fernando-perez.html
    pdfs/
        post01-deb-nolan.pdf
        post01-ani-adhikari.pdf
        post01-bin-yu.pdf
        post01-phil-stark.pdf
        post01-fernando-perez.pdf
{% endhighlight %}

_Et Voilà!_. 

In case you are curious (and have some free time), you can find the 
the blog posts prepared by the Stat 133 students in the following github 
repository:

[https://github.com/ucb-stat133/stat133-posts-fall17](https://github.com/ucb-stat133/stat133-posts-fall17)

Happy file conversion!

