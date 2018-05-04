---
layout: post
category: python
tags: [python, openoffice, libreoffice, scripting]
title: OpenOffice docs and Python
---

# Writing ODT by Python
When creating my little Python utility I realized, that writing data to txt via print (console) will not be enough.  To make my [pinmix](https://github.com/lukaszkuczynski/pinmix) little more useful I wanted to create the way of clear presentation of a textual data.  The first thought was to create a HTML with contents, using classes and divs.  But, I usually use Word-like tools to edit and modify my documents.  They have this nice option of posting comments and so on.  And overall this is more straightforward way of managing text documents, right?  So how to create OpenOffice doc with Python?  It is so easy using `odfpy` module.

# Solution
So this is basically how I did that using Python.  You are just to import some libraries and voila, happy editing.
Having separate styles defined you can then do the batch-updates of all of the similar text parts.  It sounds like fast document adaptation, fitting all your needs.
{% gist e97cfab2dcbd6b5342947147816e7465 %}

# Conclusion
Once again I understood, having Python in your toolset you are always just few lines away from advanced solutions.
