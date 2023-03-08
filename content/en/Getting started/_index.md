---
title: "Getting started"
description: ""
#featured_image: '/images/banner.jpg'
menu:
  main:
    weight: 1
---

## Introduction

The Flemish Parliament makes its database available via web services: <http://ws.vlpar.be/e/opendata/api>. These data are open and free to use without restrictions. The **flempar R-package** is made for querying the web API. It parses the API responses and transforms JSON into a useful object (i.e. data frame) for the end-user.

In these posts, we are going to use the **flempar** package to collect and analyze:

* Key characteristics of Flemish members of parliament (MPs): `get_mp()`
* Parliamentary work, including speeches and documents: `get_work()`

We guide you through several specific research questions for each of these two functions to demonstrate the package's various applications and showcase the wealth of available data. After going through all blog posts, you will be able to work out how to extract other data suited to your particular research questions from the API of the Flemish Parliament. 

But first things first, let's get started!
