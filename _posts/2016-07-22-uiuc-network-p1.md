---
layout: post
title: UIUC interactive network (p1)
date: 2016-07-22
description: Scraping UIUC data from the web using BeautifulSoup (who comes up with these names?).
---

Part of project: [UIUC Affiliation Network](http://data-slinky.com/project/2_UIUC_affiliation_network/)

# Lets create a UIUC network visualization!

One of the benefits of being a student at a public university is that university-related
data is publically available such as [how much people are making](http://salaryguide.dailyillini.com/#/).
As one of the largest public institution in the US, I thought it would be interesting to
examine the affiliation network of the employees at UIUC. In this series of posts, I will 
walk you  through the process of how I went about creating a visualization of the UIUC 
network.

# 1. Obtaining the data

The data for the project is available from the [Grey Book](http://www.bot.uillinois.edu/grey-book). 
We will use the 2015-2016 salary data which contains the college and departmental 
affiliations of all faculty, staff and university administrators. The data is available as a PDF or a collection of HTML pages. We will scrape the data from the latter as OCR is more complicated.

We will use Python to scrape the data. [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/) 
is a great library to scrape HTML and the module [urlib.request](https://docs.python.org/3.0/library/urllib.request.html) 
will be used to open URLs. We will also be using regular expressions with 
[re](https://docs.python.org/3/library/re.html) and [json](https://docs.python.org/3/library/json.html) 
to output our data.

Let us first navigate to the .html file and parse the html  file.

```python
from bs4 import BeautifulSoup
from urllib.request import urlopen
import re

weblink = "http://www.trustees.uillinois.edu/trustees/resources/15-16-Graybook/"
webpage = urlopen(weblink + "TOC.html")
soup = BeautifulSoup(webpage, "html.parser")
mydivs = soup.findAll("table", { "summary" : re.compile(".*Urbana-Champaign.*")})
```

The function `urlopen` allows us to open the url of the website. We use the `findAll` function to find the _table_ tag for the the Urbana-Champaign campus. Note the use of `compile` to find the specific character on the summary description.

```python
for div in mydivs:
    links = div.findAll('a')
```

Since _TOC.html_ contains multiple links to different departments, we need to obtain all 
of the links. We do so by iterating through the summary table, finding the __a href__ tags 
and storing the urls to the list _links_.

The next few lines looks convoluted but it's quite straightforward. First, we create a 
file to output the data to once scraped. We then iterate through each of the links obtained 
from the previous step. Again, _urlopen_ is used open the link for parsing. The table of
interest this time is the table of salaries which looks like this:

<img src="/img/salary_table_img.png" alt="salary_table" height="80%" width="80%">


The corresponding html code is as follows:

![](/img/salary_table_code.png "salary_table_code")

Now, it is clear that we need the _class_ called "dept-heading" and "empl" from the table. 
We can obtain the cells by iterating through the table with `findAll`, storing the department 
if it has a number in the beginning (using RegEx). Below is the code.

```python
f = open("uiucData.txt", "w+")

for a in links:
    subpage = urlopen(weblink + a['href'])
    soup2 = BeautifulSoup(subpage, "html.parser")
    table = soup2.find("table", {"summary": "Table of Salary Ranges"})
    for row in table.findAll("th", {"class": ["dept-heading", "empl"]}):
        if re.match("\d+\s-\s", row.text):
            department = re.sub("\d+\s-\s", "", row.text)
        else:
            prof = row.text.strip()
            print(department+"\t"+prof, file=f)
f.close()
```

We print the result to the file labeled _uiucData.txt_ and that's it! This file will serve 
as an edgelist for our graph.

In the upcoming posts, we will discuss how to ingest the data to create a graph, followed 
by creating an interactive network.