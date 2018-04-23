---
layout: post
category: python
tags: [python, setuptools, setup, script, share]
title: Sharing you Python with setupttols
---

# Problem
The previous post was to show how I was able to help myself with inline pinyin-ization of text saved in file.  But how to share that?  I could tell to install requirements using `requirements.txt` file and then run correct script with `python script params`. But.. many of the Python tools are handy just because they are kind of independent tools, you can run in your Bash directly, f.e. `pip`, `pytest` to name just few.  I decided to externalize my small module as a setup package.  Later on I have hope to put it on the external website for Python modules, thus allowing it just to `pip install ..`.

# Solution
Python comes with `setuptools` package that gives you possibility to package whole folders with all the files needed to run the whole machinery.  You can have tests and source files stored in different folders and tied by package.  This is exemplary package definition.

{% highlight python %}
{% github_sample /lukaszkuczynski/pinmix/master/setup.py %}
{% endhighlight %}


We have some of the options available:
 - `name`
 - `packages`: which folders to include in output package
 - `install_requires`: what are required packages installed automatically when package is installed
 - `entry_points`: what script will be installed on your Python path to make running of your module easier.

Having entrypoint defined we can run it directly from the command line.  

There is one more option to define entrypoint, but this which I used here seems to be easier, and it is fully testable.  You can just import the `cmdline` module and test it as you would do with any other module.  The example of my `cmdline` is below
{% highlight python %}
{% github_sample /lukaszkuczynski/pinmix/master/pinmix/cmdline.py %}
{% endhighlight %}
Project is build and managed by Travis.  We will come back to that nice CD tool soon.

# TL;DR
Using setuptools I was able to provide testable small module that you can easilyt integrate with your system and call using short command `pinmix`.

