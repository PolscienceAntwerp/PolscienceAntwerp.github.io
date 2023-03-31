---
title: Retrieving parliamentary work
author: ["Evelien Willems", "Frederik Heylen"]
featured_image: '/images/c_feature19.png'
banner: '/images/banner3.jpg'

# Summary for listings and search engines
summary: MPs at work. 

# Link this post with a project
projects: []

# Date published
date: '2022-09-25T00:00:00Z'

# Date updated
lastmod: '2022-09-25T00:00:00Z'

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

# tags:
#  - Academic
#  - 开源

# categories:
#  - Demo
#  - 教程
---


In this post we delve into the `get_work()` function and the parameters you can specify to query for more specific information.

### Example 1 - Basic functionalities

First, we will query for all debates on current affairs (one possible type of parliamentary work - and the default option) organized in January-August 2022. We use the `get_work()` function. Indeed, in contrast to the `get_mp` function, you *always* need to specify a date range using the following arguments:

* date_range_from: the start date YYYY-MM-DD
* date_range_to: the end date YYYY-MM-DD

```r
work_b <- get_work(date_range_from="2022-01-01"
                    , date_range_to="2022-08-31")
```
```r
work_b %>%
  tibble() %>%
  head(10) %>% View
```
As an enormous amount of data can be retrieved through the `get_work()` function, it is highly **recommended to test your query on a limited date range** before expanding the date range to comprise multiple months to years.

Note also that we include a chunk of code that lets you inspect the first 10 observations in the data frame created. This allows you to get a feel of the data obtained before diving in deeper. This is how the resulting data frame looks like: 

{{< figure src="/images/DefaultOutput_get_work.png" title="Default output `get_work()`" >}}

Inspecting this data frame, shows you what the default parameters deliver. Namely, the "details" about "debates" in current affairs in "plenary" sessions for the "dates" specified. Though, much more is possible!

The `get_work()` function has the following arguments:

* date_range_from: the start date YYYY-MM-DD
* date_range_to: the end date YYYY-MM-DD
* fact: options include "written_questions", "debates", "oral_questions_and_interpellations", "parliamentary_initiatives", and "council_hearings"
* type: options include "details", "speech" and "documents"
* plen_comm: choose between plenary "plen" and commission "comm" sessions
* use_parallel: Boolean of which the default value is set to TRUE, select FALSE in case you do not have multiple workers to speed up the calls made
* raw: Boolean of which the default value is set to FALSE, select TRUE in case you wish to retrieve the unprocessed data
* extra_via_fact: Boolean of which the default value is set to FALSE, select TRUE in case you wish to retrieve the unique identifiers of documents outside the date range specified but linked to the "fact"
* two_columns_pdf: Boolean of which the default value is set to FALSE, select TRUE in case you encounter PDFs divided into two text columns. A standard method cannot deal with this, this method ensures that this is read correctly

So let's, for instance, query for some specific type of parliamentary work conducted in 2021. Here, we opt to retrieve data about parliamentary initiatives (`fact="parliamentary_initiatives"`) discussed in the plenary (`plen_comm="plen"`) sessions (`date_range_from="2021-01-01"`and `date_range_to="2021-12-31"`). We also specify that we are interested in the `type="details"` as this will deliver us a data frame containing the essentials about each parliamentary initiative. 

```r
pi_work <- get_work(date_range_from="2021-01-01"
                    , date_range_to="2021-12-31"
                    , fact="parliamentary_initiatives"
                    , type="details"
                    , plen_comm="plen")
```
```r
pi_work %>%
  tibble() %>%
  head(10) %>% View
```
Now we can inspect the data a bit more. Something you can check, for example, is which topics are covered in the parliamentary initiatives at that time. We use some `dplyr` code, in this case `count()` to quickly count the unique values in the column `result_thema_1`.

```r
pi_work %>% count(result_thema_1, sort = T) -> work_topics
work_topics
```

### Example 2 - Retrieving *documents* related to parliamentary work

Here, we opt to retrieve data about written questions (`fact="written questions"`) issued in March 2022 (`date_range_from="2022-03-01"`and `date_range_to="2022-03-31"`). Importantly, we specify that we are interested in `type="document"` as this will deliver us a data frame containing the documents related to each written question in the selection.

```r
wq_work_doc <- get_work(date_range_from="2022-03-01"
                    , date_range_to="2022-03-31"
                    , fact="written_questions"
                    , type="document")  
```
```r
wq_work_doc %>%
  tibble::tibble() %>%
  head(10) %>% View
```

This call delivers you a data frame with (at least) the following columns:

* id_fact: unique identifier linking to the specific parliamentary activity the document is associated with (here, the written question)
* publicatiedatum: date the document was published
* text: each row in this column contains the text contained in the document

The data frame can be manipulated further to make it ready for text analysis. First, matching the data frame with the `details` of its associated written questions is possible via `id_fact`.

```r
wq_work <- get_work(date_range_from="2022-03-01"
                    , date_range_to="2022-03-31"
                    , fact="written_questions"
                    , type="details")  
```

```r
wq_docs_details <- left_join(wq_work, wq_work_doc, by=c("id_fact")) 
```

Another element here is that we can **search** these documents for **specific key words** occurring in one of them. For this, we use the `search_terms()` function with the following arguments: 

* text_field: fill in the column name of the text field in c("XXX"), multiple columns can be specified at once as c("XXX", "XXX", ...)
* search_terms: fill in the term(s) you want the documents checked for 

Here, we opt to retrieve data about the PFOS/PFAS debate in Belgium (`search_terms = c("PFOS", "PFAS")`).

```r
wq_docs_details %>%
  as_tibble() %>%
  search_terms(text_field = c("text"), search_terms = c("PFOS", "PFAS")) -> PFOS
```

Note that to get a good feel of the data, it is often recommended to sort the data frame by `id_fact` (e.g. when retrieving documents about `fact="parliamentary_initiatives`, multiple documents are associated to one initiative). This thus gives you a clearer overview of all documents related to one unique 'activity'. 

### Example 3 - Retrieving *speech* related to parliamentary work

For this example, we opt to retrieve data about oral questions and interpellations (`fact="oral_questions_and_interpellations"`) discussed in the committee meetings (`plen_comm="comm"`) of March 2022 (`date_range_from="2022-03-01"`and `date_range_to="2022-03-31"`). Next, we specify that we are interested in the `type="speech"` as this will deliver us a data frame containing the 'speech' or spoken word by MPs and ministers related to each oral question in the selection. 

```r
oq_speech <- get_work(date_range_from="2022-03-01"
                    , date_range_to="2022-03-31"
                    , fact="oral_questions_and_interpellations"
                    , type="speech"
                    , plen_comm="comm") 
```

This call delivers you a data frame with the following columns:
* journaallijn_id: the unique identifier for each action-reaction sequence related to the same oral question.
* text: each row in this column contains the 'speech' per individual speaker.
* sprekertitel: identifies who is speaking.
* persoon_id: the unique identifier for each individual.
* id_fact: unique identifier linking to the specific parliamentary activity the speech is associated with (here, oral questions and interpellations).

Matching this data frame with the `details` of these oral questions is possible via the `journaallijn_id`. First, we get the details of the oral questions and interpellations.

```r
oq_details <- get_work(date_range_from="2022-03-01"
                    , date_range_to="2022-03-31"
                    , fact="oral_questions_and_interpellations"
                    , type="details"
                    , plen_comm="comm") 
```
Now we are ready to join the `oq_speech`data frame with the `oq_details` data frame containing the 'speech' per oral question. We do this based on a combination of two unique identifiers: `id_fact` and `journaallijn_id`.

```r
questions_speech <- left_join(oq_speech, oq_details, by=c("id_fact", "journaallijn_id")) 
```

Finally, we can also **search** these spoken words for **specific key words** occurring. We again use the `search_terms()` function. We opt to retrieve data about the PFOS/PFAS debate in Belgium (`search_terms = c("PFOS", "PFAS")`). 

```r
questions_speech %>%
  as_tibble() %>%
  search_terms(text_field = c("text"), search_terms = c("PFOS", "PFAS")) -> PFAS_oq
```



