# Indeed Web Scraping

## Description
This project was created as a web scraping assignment for the course Advanced Web Apps for Communigators at the University of Florida.

In this project, over 300 rows of data were collected for job listings on indeed. The information gathered included: job name, company name, job location, and job link.

The initial proposal for this assignment included gathering job title, company name, location, link, pay, job type, and job perks/benefits for around 300 job entries. However, in this project, I decided not to collect the additional pay, job type, or job perks/benefits.

## Process

To begin, import the following at the top of your document. This project requires Beautiful Soup and Selenium to function properly.

### Imports

Import the following items for Beautiful Soup
```bash
from bs4 import BeautifulSoup
import requests
import time
```

Import the following items for Selenium
```bash
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.support.ui import Select
from selenium.webdriver.common.by import By
```
After importing the above, we can begin to gather information from the site.

### Steps

For the purpose of this assignment, I chose the to only search for front-end web developer jobs in the state of Florida, this resulted in me using the following link as the basis for my web scraping: [https://www.indeed.com/jobs?q=front+end+developer&l=Florida&from=searchOnHP&vjk=9daa6da0b5835660](https://www.indeed.com/jobs?q=front+end+developer&l=Florida&from=searchOnHP&vjk=9daa6da0b5835660).

Now that we know what page we are scraping from, we must declare the following variables so the browser understands our starting point.

```python
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))
driver.get('https://www.indeed.com/jobs?q=front+end+developer&l=Florida&from=searchOnHP&vjk=9daa6da0b5835660');
```


Next, we create the following empty lists to hold the data we are scraping
```python
job_list = [] #this will hold the job titles
company_list = [] #this will hold the company names
location_list = [] #this will hold the job location
link_list = [] #this will hold the link for the job application and information on indeed
```
After inspecting the page it can be seen that all the job listings on each page are held in <li> elements, and the information needed are located in tables within each list.
So, I defined a variable that contains the specific sections of the html made from the soup.

```python
tables = soup.find_all("td", class_="resultContent")
```

### def find_job_title():
This function gathers all of the job names from each listing on the page.
I noticed that the job titles are included in a <span> element located within each <a> tag.
```python
def find_job_title():
    job_a = table.find('a') # finds the a tag within each <li> table
    job_title = job_a.find('span').text #gets ONLY the text from the <span> within and saves it to a variable
    job_list.append(job_title) #adds the job name to the empty list
```
