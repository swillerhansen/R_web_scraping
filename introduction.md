---
title: 'Introduction'
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do you write a lesson using R Markdown and `{sandpaper}`?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain how to use markdown with the new lesson template
- Demonstrate how to include pieces of code, figures, and nested challenge blocks

::::::::::::::::::::::::::::::::::::::::::::::::

## Introduction
This is a course in how to use the programming language R to do web scraping. Web scraping is a set of methods to systematically download parts of web pages by writing code in a script instead of copy-pasting from the web page.

## What makes up a web page?
A web pages is made up of elements in the HTML language. 

## Why learn to scrape instead of copy-pasting?
The advantage with using web scraping is that we can in our script specify which specific parts of the web page that we want to download by referring to those elements' HTML code. This allows for precision in what we download. 

Furthermore, the scraped content is often in a format that can more easily be handled. By scraping we do not run the risk of having the content in a weird format when it is copy-pasted into a text file or an Excel sheet that makes it unsuited for analysis

Another advantage of web scraping, which we will do in this course, is that we can scrape the same element on multiple pages. i.e. instead of opening each page separately and for each page marking the parts of the page that we want to download and copy-pasting it, we can write a script that will scrape the same HTML element on multiple pages. This allows for speed and consistency

## Am I allowed to scrape?
Not all web pages allow for scraping. Just because you can scrape doesn't mean that you're allowed to. It is necessary to read the terms and conditions for scraping. 

Sometimes scraping rules differ depending on who does the scraping. E.g. many web pages allow search engines to scrape them in order to index them in the search engines index, but do not allow others to scrape their pages. Sometimes 

Some organizations allow scraping of some of their web pages, but not all of them. E.g. they may allow scraping of their front page, but not of sub-pages. Or they allow scraping of a few of their sub-pages but not all their other sub-pages on their front page. 

Fortunately for us, we don't have to read through this ourselves. The package `polite` has a function called `bow()`. This function ensures that we only scrape web pages that allow scraping. When we run it on a URL, if it gives the message `The path is scrapable for this user-agent` it means that we are allowed to scrape. If it gives the message `The path is not scrapable for this user-agent` it means that scraping is not allowed. We can integrate `polite` and `bow()` into our workflow so that if we run the function before we scrape the web page, it will prevent us from scraping and thus ensure that we don't do something that we're not allowed to. 
