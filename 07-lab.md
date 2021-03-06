Lab 07 - Web scraping and Regular Expressions
================

``` r
knitr::opts_chunk$set(include  = TRUE)
```

# Learning goals

  - Use a real world API to make queries and process the data.
  - Use regular expressions to parse the information.
  - Practice your GitHub skills.

# Lab description

In this lab, we will be working with the [NCBI
API](https://www.ncbi.nlm.nih.gov/home/develop/api/) to make queries and
extract information using XML and regular expressions. For this lab, we
will be using the `httr`, `xml2`, and `stringr` R packages.

This markdown document should be rendered using `github_document`
document.

``` r
#install.packages("httr")
#install.packages("xml2")
#install.packages("stringr")
```

## Question 1: How many sars-cov-2 papers?

Build an automatic counter of sars-cov-2 papers using PubMed. You will
need to apply XPath as we did during the lecture to extract the number
of results returned by PubMed in the following web address:

    https://pubmed.ncbi.nlm.nih.gov/?term=sars-cov-2

Complete the lines of code:

``` r
# Downloading the website
website <- xml2::read_html("https://pubmed.ncbi.nlm.nih.gov/?term=sars-cov-2")

#alternative 
website2 <- httr::GET(
  url ="https://pubmed.ncbi.nlm.nih.gov",
  query=list(term="sars-cov-2")
)

# Finding the counts
counts <- xml2::xml_find_first(website, "/html/body/main/div[9]/div[2]/div[2]/div[1]/span")

# Turning it into text
counts <- as.character(counts)

# Extracting the data using regex
stringr::str_extract(counts, "[0-9,]+")
```

    ## [1] "33,814"

Don’t forget to commit your work\! The number of articles related to
sars-cov-2 is 33,814.

## Question 2: Academic publications on COVID19 and Hawaii

You need to query the following The parameters passed to the query are
documented [here](https://www.ncbi.nlm.nih.gov/books/NBK25499/).

Use the function `httr::GET()` to make the following query:

1.  Baseline URL:
    <https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi>

2.  Query parameters:
    
      - db: pubmed
      - term: covid19 hawaii
      - retmax: 1000

<!-- end list -->

``` r
library(httr)
```

    ## Warning: package 'httr' was built under R version 4.0.2

``` r
query_ids <- GET(
  url   = "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi",
  query = list(
    db = "pubmed",
    term = "covid19 hawaii",
    retmax = 1000
    )
)
#query_ids

# Extracting the content of the response of GET
ids <- httr::content(query_ids)
#ids
```

The query will return an XML object, we can turn it into a character
list to analyze the text directly with `as.character()`. Another way of
processing the data could be using lists with the function
`xml2::as_list()`. We will skip the latter for now.

Take a look at the data, and continue with the next question (don’t
forget to commit and push your results to your GitHub repo\!).

## Question 3: Get details about the articles

The Ids are wrapped around text in the following way: `<Id>... id number
...</Id>`. we can use a regular expression that extract that
information. Fill out the following lines of code:

``` r
# Turn the result into a character vector
ids <- as.character(ids)
cat(ids)
```

    ## <?xml version="1.0" encoding="UTF-8"?>
    ## <!DOCTYPE eSearchResult PUBLIC "-//NLM//DTD esearch 20060628//EN" "https://eutils.ncbi.nlm.nih.gov/eutils/dtd/20060628/esearch.dtd">
    ## <eSearchResult>
    ##   <Count>40</Count>
    ##   <RetMax>40</RetMax>
    ##   <RetStart>0</RetStart>
    ##   <IdList>
    ##     <Id>32984015</Id>
    ##     <Id>32969950</Id>
    ##     <Id>32921878</Id>
    ##     <Id>32914097</Id>
    ##     <Id>32914093</Id>
    ##     <Id>32912595</Id>
    ##     <Id>32907823</Id>
    ##     <Id>32907673</Id>
    ##     <Id>32888905</Id>
    ##     <Id>32881116</Id>
    ##     <Id>32837709</Id>
    ##     <Id>32763956</Id>
    ##     <Id>32763350</Id>
    ##     <Id>32745072</Id>
    ##     <Id>32742897</Id>
    ##     <Id>32692706</Id>
    ##     <Id>32690354</Id>
    ##     <Id>32680824</Id>
    ##     <Id>32666058</Id>
    ##     <Id>32649272</Id>
    ##     <Id>32596689</Id>
    ##     <Id>32592394</Id>
    ##     <Id>32584245</Id>
    ##     <Id>32501143</Id>
    ##     <Id>32486844</Id>
    ##     <Id>32462545</Id>
    ##     <Id>32432219</Id>
    ##     <Id>32432218</Id>
    ##     <Id>32432217</Id>
    ##     <Id>32427288</Id>
    ##     <Id>32420720</Id>
    ##     <Id>32386898</Id>
    ##     <Id>32371624</Id>
    ##     <Id>32371551</Id>
    ##     <Id>32361738</Id>
    ##     <Id>32326959</Id>
    ##     <Id>32323016</Id>
    ##     <Id>32314954</Id>
    ##     <Id>32300051</Id>
    ##     <Id>32259247</Id>
    ##   </IdList>
    ##   <TranslationSet>
    ##     <Translation>
    ##       <From>covid19</From>
    ##       <To>"COVID-19"[Supplementary Concept] OR "COVID-19"[All Fields] OR "covid19"[All Fields]</To>
    ##     </Translation>
    ##     <Translation>
    ##       <From>hawaii</From>
    ##       <To>"hawaii"[MeSH Terms] OR "hawaii"[All Fields]</To>
    ##     </Translation>
    ##   </TranslationSet>
    ##   <TranslationStack>
    ##     <TermSet>
    ##       <Term>"COVID-19"[Supplementary Concept]</Term>
    ##       <Field>Supplementary Concept</Field>
    ##       <Count>27206</Count>
    ##       <Explode>N</Explode>
    ##     </TermSet>
    ##     <TermSet>
    ##       <Term>"COVID-19"[All Fields]</Term>
    ##       <Field>All Fields</Field>
    ##       <Count>55053</Count>
    ##       <Explode>N</Explode>
    ##     </TermSet>
    ##     <OP>OR</OP>
    ##     <TermSet>
    ##       <Term>"covid19"[All Fields]</Term>
    ##       <Field>All Fields</Field>
    ##       <Count>794</Count>
    ##       <Explode>N</Explode>
    ##     </TermSet>
    ##     <OP>OR</OP>
    ##     <OP>GROUP</OP>
    ##     <TermSet>
    ##       <Term>"hawaii"[MeSH Terms]</Term>
    ##       <Field>MeSH Terms</Field>
    ##       <Count>7799</Count>
    ##       <Explode>Y</Explode>
    ##     </TermSet>
    ##     <TermSet>
    ##       <Term>"hawaii"[All Fields]</Term>
    ##       <Field>All Fields</Field>
    ##       <Count>27601</Count>
    ##       <Explode>N</Explode>
    ##     </TermSet>
    ##     <OP>OR</OP>
    ##     <OP>GROUP</OP>
    ##     <OP>AND</OP>
    ##     <OP>GROUP</OP>
    ##   </TranslationStack>
    ##   <QueryTranslation>("COVID-19"[Supplementary Concept] OR "COVID-19"[All Fields] OR "covid19"[All Fields]) AND ("hawaii"[MeSH Terms] OR "hawaii"[All Fields])</QueryTranslation>
    ## </eSearchResult>

``` r
# Find all the ids 
ids <- stringr::str_extract_all(ids, "<Id>[0-9]+</Id>") 
ids <- ids[[1]]

# Remove all the leading and trailing <Id> </Id>. Make use of "|"?
ids <- stringr::str_remove_all(ids, "<Id>|</Id>")
```

With the ids in hand, we can now try to get the abstracts of the papers.
As before, we will need to coerce the contents (results) to a list
using:

1.  Baseline url:
    <https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi>

2.  Query parameters:
    
      - db: pubmed
      - id: A character with all the ids separated by comma, e.g.,
        “1232131,546464,13131”(‘paste(id)’)
      - retmax: 1000
      - rettype: abstract

**Pro-tip**: If you want `GET()` to take some element literal, wrap it
around `I()` (as you would do in a formula in R). For example, the text
`"123,456"` is replaced with `"123%2C456"`. If you don’t want that
behavior, you would need to do the following `I("123,456")`.

``` r
publications <- GET(
  url   = "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi",
  query = list(
    db="pubmed",
    id= paste(ids, collapse = ","),
    retmax=1000,
    rettype="abstract"
    )
)

# Turning the output into character vector
publications <- httr::content(publications)
publications_txt <- as.character(publications)
```

With this in hand, we can now analyze the data. This is also a good time
for committing and pushing your work\!

## Question 4: Distribution of universities, schools, and departments

Using the function `stringr::str_extract_all()` applied on
`publications_txt`, capture all the terms of the form:

1.  University of …
2.  … Institute of …

Write a regular expression that captures all such instances

``` r
library(stringr)
```

    ## Warning: package 'stringr' was built under R version 4.0.2

``` r
institution <- str_extract_all(
  publications_txt,
  "University of\\s[[:alpha:]]+|[[:alpha:]]+\\sInstitute of [[:alpha:]]+"
  ) 

str(publications_txt)
```

    ##  chr "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<!DOCTYPE PubmedArticleSet PUBLIC \"-//NLM//DTD PubMedArticle, 1st "| __truncated__

``` r
institution <- unlist(institution)
table(institution)
```

    ## institution
    ##      Australian Institute of Tropical Massachusetts Institute of Technology 
    ##                                     9                                     1 
    ##   National Institute of Environmental    Prophylactic Institute of Southern 
    ##                                     3                                     2 
    ##                 University of Arizona              University of California 
    ##                                     2                                     6 
    ##                 University of Chicago                University of Colorado 
    ##                                     1                                     1 
    ##                   University of Hawai                  University of Hawaii 
    ##                                    20                                    38 
    ##                  University of Health                University of Illinois 
    ##                                     1                                     1 
    ##                    University of Iowa                University of Lausanne 
    ##                                     4                                     1 
    ##              University of Louisville                University of Nebraska 
    ##                                     1                                     5 
    ##                  University of Nevada                     University of New 
    ##                                     1                                     2 
    ##            University of Pennsylvania              University of Pittsburgh 
    ##                                    18                                     5 
    ##                 University of Science                   University of South 
    ##                                    14                                     1 
    ##                University of Southern                  University of Sydney 
    ##                                     1                                     1 
    ##                   University of Texas                     University of the 
    ##                                     5                                     1 
    ##                    University of Utah               University of Wisconsin 
    ##                                     2                                     3

Repeat the exercise and this time focus on schools and departments in
the form of

1.  School of …
2.  Department of …

And tabulate the results

``` r
library(stringr)
schools_and_deps <- str_extract_all(
  publications_txt,
  "School\\s+of\\s+[[:alpha:]]+|Department\\s+of\\s+[[:alpha:]]+"
  )
table(unlist(schools_and_deps))
```

    ## 
    ## Department of Anesthesiology        Department of Biology 
    ##                            6                            3 
    ##     Department of Cardiology           Department of Cell 
    ##                            1                            4 
    ##       Department of Clinical  Department of Communication 
    ##                            2                            1 
    ##  Department of Computational       Department of Critical 
    ##                            1                            2 
    ##        Department of Defense  Department of Environmental 
    ##                            1                            1 
    ##   Department of Epidemiology   Department of Experimental 
    ##                            9                            1 
    ##         Department of Family        Department of Genetic 
    ##                            3                            1 
    ##      Department of Geography     Department of Infectious 
    ##                            2                            2 
    ##    Department of Information       Department of Internal 
    ##                            1                            6 
    ##        Department of Medical       Department of Medicine 
    ##                            3                           44 
    ##   Department of Microbiology         Department of Native 
    ##                            1                            2 
    ##     Department of Nephrology      Department of Neurology 
    ##                            5                            1 
    ##      Department of Nutrition             Department of OB 
    ##                            4                            5 
    ##     Department of Obstetrics Department of Otolaryngology 
    ##                            4                            4 
    ##     Department of Pediatrics       Department of Physical 
    ##                           13                            3 
    ##     Department of Population     Department of Preventive 
    ##                            1                            2 
    ##     Department of Psychiatry     Department of Psychology 
    ##                            4                            1 
    ##   Department of Quantitative Department of Rehabilitation 
    ##                            6                            1 
    ##         Department of Social        Department of Surgery 
    ##                            1                            6 
    ##  Department of Translational       Department of Tropical 
    ##                            1                            5 
    ##           Department of Twin        Department of Urology 
    ##                            2                            1 
    ##       Department of Veterans           School of Medicine 
    ##                            2                           87 
    ##            School of Natural            School of Nursing 
    ##                            1                            1 
    ##             School of Public             School of Social 
    ##                           20                            1

## Question 5: Form a database

We want to build a dataset which includes the title and the abstract of
the paper. The title of all records is enclosed by the HTML tag
`ArticleTitle`, and the abstract by `Abstract`.

Before applying the functions to extract text directly, it will help to
process the XML a bit. We will use the `xml2::xml_children()` function
to keep one element per id. This way, if a paper is missing the
abstract, or something else, we will be able to properly match PUBMED
IDS with their corresponding records.

``` r
pub_char_list <- xml2::xml_children(publications)
pub_char_list <- sapply(pub_char_list, as.character)
```

Now, extract the abstract and article title for each one of the elements
of `pub_char_list`. You can either use `sapply()` as we just did, or
simply take advantage of vectorization of
`stringr::str_extract`

``` r
abstracts <- str_extract(pub_char_list[1], "<Abstract>(\\n|.)+</Abstract>")
abstracts <- str_remove_all(abstracts, "</?[[:alnum:]]+>")
abstracts <- str_replace_all(abstracts, "\\s+"," ")
```

How many of these don’t have an abstract? Now, the
title

``` r
titles <- str_extract(pub_char_list, "<ArticleTitle>(\\n|.)+</ArticleTitle>")
titles <- str_remove_all(titles, "</?[[:alnum:]]+>")
titles <- str_replace_all(titles, "\\s+"," ")
```

Finally, put everything together into a single `data.frame` and use
`knitr::kable` to print the results

``` r
database <- data.frame(
  PubMedID=ids,
  Title=titles,
  Abstracts=abstracts
)
knitr::kable(database)
```

| PubMedID | Title                                                                                                                                                           | Abstracts                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| :------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 32984015 | Perspective: Cancer Patient Management Challenges During the COVID-19 Pandemic.                                                                                 | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32969950 | Workflow Solutions for Primary Care Clinic Recovery During the COVID-19 Pandemic: A Primer.                                                                     | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32921878 | Will COVID-19 be one shock too many for smallholder coffee livelihoods?                                                                                         | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32914097 | Spotlight on Nursing: Navigating Uncharted Waters: Preparing COVID-19 Capable Nurses to Work in a Transformed Workplace.                                        | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32914093 | Public Compliance with Face Mask Use in Honolulu and Regional Variation.                                                                                        | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32912595 | Ensuring mental health access for vulnerable populations in COVID era.                                                                                          | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32907823 | Evidence based care for pregnant women with covid-19.                                                                                                           | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32907673 | Prediction and severity ratings of COVID-19 in the United States.                                                                                               | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32888905 | Saliva is a reliable, non-invasive specimen for SARS-CoV-2 detection.                                                                                           | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32881116 | Delivering Prolonged Exposure Therapy via Videoconferencing During the COVID-19 Pandemic: An Overview of the Research and Special Considerations for Providers. | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32837709 | Comparison of Telehealth-Related Ethics and Guidelines and a Checklist for Ethical Decision Making in the Midst of the COVID-19 Pandemic.                       | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32763956 | Reactive arthritis after COVID-19 infection.                                                                                                                    | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32763350 | 3D printing of face shields to meet the immediate need for PPE in an anesthesiology department during the COVID-19 pandemic.                                    | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32745072 | Obstetric hospital preparedness for a pandemic: an obstetric critical care perspective in response to COVID-19.                                                 | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32742897 | COVID-19 outbreak on the Diamond Princess Cruise Ship in February 2020.                                                                                         | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32692706 | Academic clinical learning environment in obstetrics and gynecology during the COVID-19 pandemic: responses and lessons learned.                                | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32690354 | Role of modelling in COVID-19 policy development.                                                                                                               | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32680824 | Modelling insights into the COVID-19 pandemic.                                                                                                                  | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32666058 | The Daniel K. Inouye College of Pharmacy Scripts: Panic or Panacea, Changing the Pharmacist’s Role in Pandemic COVID-19.                                        | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32649272 | Combating COVID-19 and Building Immune Resilience: A Potential Role for Magnesium Nutrition?                                                                    | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32596689 | Viewpoint: Pacific Voyages - Ships - Pacific Communities: A Framework for COVID-19 Prevention and Control.                                                      | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32592394 | A role for selenium-dependent GPX1 in SARS-CoV-2 virulence.                                                                                                     | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32584245 | Communicating Effectively With Hospitalized Patients and Families During the COVID-19 Pandemic.                                                                 | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32501143 | The Impact of the COVID-19 Pandemic on Vulnerable Older Adults in the United States.                                                                            | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32486844 | Covid-19 and Diabetes in Hawaii.                                                                                                                                | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32462545 | Treatments Administered to the First 9152 Reported Cases of COVID-19: A Systematic Review.                                                                      | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32432219 | Insights in Public Health: COVID-19 Special Column: The Crisis of Non-Communicable Diseases in the Pacific and the Coronavirus Disease 2019 Pandemic.           | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32432218 | COVID-19 Special Column: COVID-19 Hits Native Hawaiian and Pacific Islander Communities the Hardest.                                                            | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32432217 | COVID-19 Special Column: Principles Behind the Technology for Detecting SARS-CoV-2, the Cause of COVID-19.                                                      | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32427288 | ACE2 receptor expression in testes: implications in coronavirus disease 2019 pathogenesis†.                                                                     | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32420720 | Caring for Critically Ill Adults With Coronavirus Disease 2019 in a PICU: Recommendations by Dual Trained Intensivists.                                         | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32386898 | Geospatial analysis of COVID-19 and otolaryngologists above age 60.                                                                                             | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32371624 | The War on COVID-19 Pandemic: Role of Rehabilitation Professionals and Hospitals.                                                                               | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32371551 | The COronavirus Pandemic Epidemiology (COPE) Consortium: A Call to Action.                                                                                      | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32361738 | Risk Factors Associated with Clinical Outcomes in 323 COVID-19 Hospitalized Patients in Wuhan, China.                                                           | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32326959 | High-flow nasal cannula may be no safer than non-invasive positive pressure ventilation for COVID-19 patients.                                                  | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32323016 | SAGES and EAES recommendations for minimally invasive surgery during COVID-19 pandemic.                                                                         | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32314954 | COVID-19 Community Stabilization and Sustainability Framework: An Integration of the Maslow Hierarchy of Needs and Social Determinants of Health.               | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32300051 | Insights from immuno-oncology: the Society for Immunotherapy of Cancer Statement on access to IL-6-targeting therapies for COVID-19.                            | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |
| 32259247 | Pain Management Best Practices from Multispecialty Organizations During the COVID-19 Pandemic and Public Health Crises.                                         | On March 11, 2020, the WHO has declared the coronavirus disease 2019 (COVID-19) a global pandemic. As the last few months have profoundly changed the delivery of health care in the world, we should recognize the effort of numerous comprehensive cancer centers to share experiences and knowledge to develop best practices to care for oncological patients during the COVID-19 pandemic. Patients as well as physicians must be aware of all these constraints and profound social, personal, and medical challenges posed by the tackling of this deadly disease in everyday life in order to adjust to such a completely novel scenario. This review will discuss facing the challenges and the current approaches that cancer centers in Italy and United States are adopting in order to cope with clinical and research activities. Copyright © 2020 Terracciano, Buonerba, Scafuri, De Berardinis, Calin, Ferrajoli, Fabbri and Cimmino. |

Done\! Knit the document, commit, and push.

## Final Pro Tip (optional)

You can still share the HTML document on github. You can include a link
in your `README.md` file as the following:

``` md
View [here](https://ghcdn.rawgit.org/:user/:repo/:tag/:file)
```

For example, if we wanted to add a direct link the HTML page of lecture
7, we could do something like the
following:

``` md
View [here](https://ghcdn.rawgit.org/USCbiostats/PM566/master/static/slides/07-apis-regex/slides.html)
```
