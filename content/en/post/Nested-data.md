---
title: Working with nested data
author: ["Evelien Willems"]
featured_image: '/images/c_feature17.png'
banner: '/images/banner3.jpg'
# Summary for listings and search engines
summary: How to extract data nested in lists in lists. 

# Link this post with a project
projects: []

# Date published
date: '2023-01-30T00:00:00Z'

# Date updated
lastmod: '2023-01-30T00:00:00Z'

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: true

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

### Working with lists in lists

As you may notice from your first inspection of some of the data frames the `get_work()` function delivers, the function confronts you with a couple of *nested* columns in its output. We will go over two examples to dig up relevant information from these *lists in lists*. Keep in mind that it is recommended to go step-by-step when going deeper into the lists as it is far too easy to end up with a giant unfitting data frame when combining multiple 'unnestings' at once without knowing the exact content of the data stored within the lists.

**1. Topics by type of activity and party: unnesting of the column `contacttype`**

Let's delve into these *lists in lists* by creating a `pi_work` data frame. 

```r
pi_work <- get_work(date_range_from="2021-01-01"
                    , date_range_to="2021-12-31"
                    , fact="parliamentary_initiatives"
                    , type="details"
                    , plen_comm="plen")
```

Specifically, we will unnest columns to assess which MPs from which parties are most active in proposing new legislation and tabling resolutions.

```r
pi_work %>%
  select(id_fact, type_specifiek, contacttype) %>%
  unnest(contacttype, names_sep = "_") %>%
  unnest(contacttype_contact, names_sep = "_") %>%
  unnest(contacttype_contact_fractie, names_sep = "_") -> pi_type_party
pi_type_party
```

Now we can make a count of the observations by relevant types of activity and party or visualize the results. First, we filter out legislative proposals made by MPs ('Voorstel van decreet') and resolutions ('Voorstel van resolutie').

```r
pi_type_party %>%
  filter(type_specifiek == "Voorstel van decreet" 
         | type_specifiek == "Voorstel van resolutie") -> pi_type_party_c
```

Next, we group our data by party to arrive at a count. Alternatively, we can plot the results. 

```r
pi_type_party_c %>% 
  group_by(contacttype_contact_fractie_naam) %>%
  count(type_specifiek, sort = T) -> pi_type_party_cc
```
```r
plot6 <- ggplot(pi_type_party_cc, aes(x = reorder(contacttype_contact_fractie_naam, -n), y = n, fill = type_specifiek, colour = type_specifiek)) + 
  geom_bar(stat ="identity") +
  labs(x="", y = "Count") + 
  theme_classic() +
  theme(axis.text.x = element_text(angle = 90, vjust=.5, hjust=1)) +
  theme(legend.title = element_blank())
plot6  
```

{{< figure src="/images/plot6.png" title="" >}}

**2. Voting records: Unnesting of the column `vote` and subsequent lists in lists**

```r
pi_work %>%
  select(id_fact, vote, type_specifiek) %>%
  unnest_wider(vote, names_sep = "_") %>%
  unnest(vote_stemming) %>%
  select(id_fact,type_specifiek, `stemming-tegen`) %>%
  unnest_wider(`stemming-tegen`, names_sep="_") %>%
  select(id_fact, type_specifiek, `stemming-tegen_persoon`,
         `stemming-tegen_stem`, `stemming-tegen_zetel` ) %>%
  unnest_longer(c(`stemming-tegen_persoon`, `stemming-tegen_stem`, `stemming-tegen_zetel`)) %>%
  unnest(`stemming-tegen_persoon`) %>%
  unnest(fractie, names_sep="_") %>%
  rename(stem = `stemming-tegen_stem`, zetel = `stemming-tegen_zetel`) %>%
  select(id_fact, type_specifiek, fractie_id, fractie_naam, `fractie_zetel-aantal`, naam, voornaam, stem) -> votes_against

votes_against <- unique(votes_against)

pi_work %>%
  select(id_fact, vote, type_specifiek) %>%
  unnest_wider(vote, names_sep = "_") %>%
  unnest(vote_stemming) %>%
  select(id_fact,type_specifiek, `stemming-voor`) %>%
  unnest_wider(`stemming-voor`, names_sep="_") %>%
  select(id_fact, type_specifiek, `stemming-voor_persoon`,
         `stemming-voor_stem`, `stemming-voor_zetel` ) %>%
  unnest_longer(c(`stemming-voor_persoon`, `stemming-voor_stem`, `stemming-voor_zetel`)) %>%
  unnest(`stemming-voor_persoon`) %>%
  unnest(fractie, names_sep="_") %>%
  rename(stem = `stemming-voor_stem`, zetel = `stemming-voor_zetel`)  %>%
  select(id_fact, type_specifiek, fractie_id, fractie_naam, `fractie_zetel-aantal`, naam, voornaam, stem) -> votes_favor

votes_favor <- unique(votes_favor)

pi_work %>%
  select(id_fact, vote, type_specifiek) %>%
  unnest_wider(vote, names_sep = "_") %>%
  unnest(vote_stemming) %>%
  select(id_fact,type_specifiek, `stemming-onthouding`) %>%
  unnest_wider(`stemming-onthouding`, names_sep="_") %>%
  select(id_fact, type_specifiek, `stemming-onthouding_persoon`,
         `stemming-onthouding_stem`, `stemming-onthouding_zetel` ) %>%
  unnest_longer(c(`stemming-onthouding_persoon`, `stemming-onthouding_stem`, `stemming-onthouding_zetel`)) %>%
  unnest(`stemming-onthouding_persoon`) %>%
  unnest(fractie, names_sep="_") %>%
  rename(stem = `stemming-onthouding_stem`, zetel = `stemming-onthouding_zetel`)  %>%
  select(id_fact, type_specifiek, fractie_id, fractie_naam, `fractie_zetel-aantal`, naam, voornaam, stem) -> votes_abstention

votes_abstention <- unique(votes_abstention)
```
```r
rbind(votes_against, votes_favor, votes_abstention) -> votes_af

votes_af %>%
  select(id_fact, fractie_naam, `fractie_zetel-aantal`, type_specifiek, stem) %>%
  group_by(id_fact, fractie_naam,`fractie_zetel-aantal`, type_specifiek) %>%
  count(stem)  %>%
  mutate(prop = n/`fractie_zetel-aantal`) -> votes_records
```
