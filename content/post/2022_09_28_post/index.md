---
title: Flempar the extra's

# Summary for listings and search engines
summary: Some nice extra's to the Flempar package. 

# Link this post with a project
projects: []

# Date published
date: '2022-09-26T00:00:00Z'

# Date updated
lastmod: '2022-09-26T00:00:00Z'

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: false

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: ''
  focal_point: ''
  placement: 2
  preview_only: false

authors:
  - Evelien Willems and Frederik Heylen

# tags:
#  - Academic
#  - 开源

# categories:
#  - Demo
#  - 教程
---

## Writing your data to a CSV file

In many cases, it might be useful to write the resulting data frame (here named `result`, but can be any name of your choosing) to a CSV file. Here's the code to do so, making use of the `write.csv2()` function. Note that we included a line on how to drop columns containing data in *list* format, a format a CSV file cannot handle. Careful inspection of your data and its formats is therefore always necessary! 

```{r eval=FALSE}
result %>%
     select_if(Negate(is.list)) %>%
     write.csv2("result.csv", row.names=FALSE)
```

The CSV file can now be found in your local folder.

## Applying *regexp* to detect patterns in text

Given the large bulks of text that can be gathered through the `get_work()` function, it might come in handy to structure this text a bit and apply *regexp* to detect patterns in the text, for instance, split up some text in multiple columns.

To illustrate this, we first query for a set of written questions (via `get_work()`, specifying `fact="written_questions"` and `type="document"`). These questions have a distinct structure: they are composed of a 'question' by an MP and an 'answer' by a minister. So let's ensure that 'question' and 'answer' are stored in separate columns instead of being one big bulk of text. To do so, we use `mutate` from `dplyr` to create the extra columns and we extract the *strings* via `str_extract` from `stringr` (more info [here](https://stringr.tidyverse.org/)). 

Note that this procedure entirely relies on identifying the *string* or sequence of words that marks the distinctive parts of a text. In our case, we identify 'ANTWOORD op vraag' (or: *ANSWER to question*) as introducing the distinction between the 'question' and 'answer' parts of the written question.

Finally, note that we use *regexp*. You can look up all possibilities [here](https://stringr.tidyverse.org/articles/regular-expressions.html).

```{r eval=FALSE}
written_questions <- get_work(date_range_from="2022-02-15"
                       , date_range_to="2022-02-20"
                       , fact="written_questions"
                       , type="document")
```
```{r eval=FALSE}
written_questions %>%
      mutate(vraag= str_extract(text, ".*(?<=ANTWOORD op )")) %>%
      mutate(antwoord= str_extract(text, "(?<=ANTWOORD op ).*") ) %>%
      write.csv2("written_questions.csv",row.names = FALSE)
```