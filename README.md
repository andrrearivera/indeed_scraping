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

### def find_company_name():
This function gathers the name of the companies hiring. The company names are included within the table element of each listing, so:

```python
def find_company_name():
    company_name = table.find('span', class_='companyName').text #gets only the text of the company name and saves it into a variable
    company_list.append(company_name) #adds the names into the empty list
```

### def find_location():
This function collects the location of each job. This information was collected from a <div> tag within the table of each listing.

```python
def find_location():
    company_location = table.find('div', class_='companyLocation').text #gets ONLY text from the div element
    location_list.append(company_location) # location adds to empty list
```

### def find_job_link():
This data wouldn't be as helpful if we didn't include the link for individuals to apply or learn new information.
The urls for each individual job listing page is a bit tricky, but follows a similar structure

"https://www.indeed.com/viewjob?jk=" + job_jk + "&tk=1gr98572ejiie801&from=serp&vjs=3"

The first and last parts of the link are the same; however the middle part is unique, similar to the id of each job listing. Within each <a> tag there is an attribute named **data-jk** which contains the specific set of numbers/letters for each listing (not the same as their ids)

```python
def find_job_link():
    job_a = table.find('a') #find the <a> tag that holds the link
    job_jk = job_a['data-jk'] #collect the unique id for each listing from the <a> tag
    link_list.append("https://www.indeed.com/viewjob?jk=" + job_jk + "&tk=1gr98572ejiie801&from=serp&vjs=3") #concoctonate the url with the job_jk to create the unique id for each job page.
```


### for table in tables:
The functions above only collected data for one job listing on the page; however there are 15 job listings per page. So to collect them all we create a loop and call each function within it to gather the data.

```python
for table in tables:
    find_job_title()
    find_company_name()
    find_location()
    find_job_link()
```


###click on next pages
After collecting the data from one page, we can repeat it on x number of pages. In my example I chose 20 as it results in around 300 job listings that I would be scraping.

So, we tell Selenium to click the "next page" button at the bottom of each page, collect the html of said page and turn it into soup to be able to call the same functions as above.

```python
for i in range(20):
    driver.find_element(By.XPATH, "//a[contains(@aria-label, 'Next Page')]").click()
    time.sleep(5)

    page = driver.page_source
    soup = BeautifulSoup(page,'html.parser')
    tables = soup.find_all("td", class_="resultContent")


#run the function again

#for i in range(20):
    for table in tables:
        find_job_title()
        find_company_name()
        find_location()
        find_job_link()
        time.sleep(.25)
```
