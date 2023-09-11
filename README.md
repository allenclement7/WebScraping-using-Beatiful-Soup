# WebScraping-using-Beatiful-Soup


Web scraping is extracting large amounts of unstructured data from websites and storing it in a structured format in a desired file/database. We’ll see how it’s done in this blog.

So how do you scrape data from the web?

Have you ever copied and pasted information from websites?
If yes, I would say you’ve already performed web-scraping in a way. But you can’t really copy and paste for say about a 100 times or even more, can you?

So let’s see how Python helps us do the same with the help of one of it’s packages – BeautifulSoup.

Step 1- Find a website that contains the information you require.
Some websites like Twitter and Facebook provide APIs for easy connectivity and access to their data. But some don’t, so you’ll have to write a code to navigate through and extract it’s content.

Remember, not every website is cool with you scraping their content. So make sure you’re aware about the website’s terms and conditions.

You can take a look at the website’s permissions by appending it’s URL with ‘/robots.txt’.
robots.txt file is known as the Robots Exclusion Protocol.

We’ll scrape-
Number of COVID-19 Cases for each Country-
from https://en.wikipedia.org/wiki/COVID-19_pandemic.

Step 2- Inspect the website.
It’s important for you to know the site’s structure to extract information that you’re interested in. Find out the html tags in which data that needs to be scraped is present.
Right click on the website and then click on inspect.

To understand and inspect the content, you need to know few HTML tags that are commonly used.
<head> headings | <p> paragraphs
<a> hyperlinks | <div> divisions on the page
<table> tables | <th> table headers
<tr> table rows | <tr> table cells
These tags can further have attributes like class, id, src, title, etc.



Step 3- Get the site’s HTML code in your Python script.
We’ll use the requests library to send an HTTP request to the website. The server will respond with HTML content of the page.

import requests 
response = requests.get("https://en.wikipedia.org/wiki/COVID-19_pandemic")
Let’s check if the request was successful or not.

response.status_code
Output- 200

Status code starting with 2 generally indicates success and codes starting with 4 or 5 indicates an error.

response.content
The response obtained will look similar to the HTML content you inspected.

Step 4- Parse HTML data with BeautifulSoup

The HTML content looks complex and confusing due to nested tags and multiple attributes. We now need BeautifulSoup to simplify our task.
BeautifulSoup is a python package for parsing HTML and XML documents. It creates parse trees and makes extracting data easy.

Let’s first import the BeautifulSoup package and create it’s object ‘soup’.

from bs4 import BeautifulSoup
soup = BeautifulSoup(response.content, 'html.parser')
soup.prettify()
prettify() function helps us view the manner in which the tags are nested.

Step 5- Filter out the required data using soup.


print(soup.title.text)
Output- COVID-19 pandemic — Wikipedia

We’ll now extract Names of the Countries and Number of Cases, Deaths and Recoveries.

table= soup.find('table', attrs={"class" : "wikitable"})
Country = []
Cases = []
Deaths = []
Recoveries = []
trs = table.select("tbody tr")[2:230]
for tr in trs:
    Country.append(tr.find_all("th", attrs = {'scope' : 'row'})[1].find('a').text)   
    tds = tr.find_all("td")
    Cases.append(tds[0].text.replace("\n", "").strip())
    Deaths.append(tds[1].text.replace("\n", "").strip())
    Recoveries.append(tds[2].text.replace("\n", "").strip())

    
Some BeautifulSoup functions used in the above code:
select() helps navigate through nested HTML tags and finally find occurrences of the desired tag.

find() returns first occurrence of the passed tag from the webpage response.
find_all() returns all occurrences for the tag we pass as a parameter.
Both find() and find_all() accept specific attributes as their second parameter.

text attribute helps us obtain the content from the tags returned by any of the above functions.

Step 6- Store extracted data.

Let’s now store this data in a pandas dataframe to make any further analysis easier.

import pandas as pd
data = list(zip(Country, Cases, Deaths, Recoveries))
COVID_data = pd.DataFrame(data, columns=['Country', 'Cases', 'Deaths', 'Recoveries'])
COVID_data.head(10)
