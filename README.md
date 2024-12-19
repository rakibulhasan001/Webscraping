# Webscraping
# Python code for Pagination scraping example from trustpilot.com 

from bs4 import BeautifulSoup as bs4
import requests
import pandas as pd
from lxml import html
import json

pages = []
names = []
review_text=[]
review_score=[]
review_date=[]
review_title=[]
reviewer_id=[]

#pages_to_scrape=292
for i in range(754, 756):
    url = ('https://www.trustpilot.com/review/therealreal.com?page={}').format(i)
    pages.append(url)
pages

for item in pages:
    page = requests.get(item)
    soup = bs4(page.text, 'html.parser')
    #print(soup.prettify())              

    for rev in soup.find_all('div',class_="styles_reviewContent__0Q2Tg"):
        nv = rev.find_all('p',class_= 'typography_body-l__KUYFJ typography_appearance-default__AAY17 typography_color-black__5LYEn')
        review = rev.p.text.strip() if len(nv) == True else '-'
        review_text.append(review)
    for rev in soup.find_all('div',class_='typography_body-m__xgxZ_ typography_appearance-subtle__8_H2l styles_datesWrapper__RCEKH'):
        review_date.append(rev.find('time').get('datetime'))           #found in two formats: title="Tuesday, January 18, 2022, 10:40:00 AM" & 
                                                                       #datetime="2022-01-18T10:40:00.000Z" class="
    for rev in soup.find_all('h2',class_='typography_heading-s__f7029 typography_appearance-default__AAY17'):
        review_title.append(rev.text.strip())
    for rev in soup.find_all('section',class_='styles_reviewContentwrapper__zH_9M'):
        review_score.append(rev.find('div').get('data-service-review-rating'))
    for rev in soup.find_all('aside',class_='styles_consumerInfoWrapper__KP3Ra'):
        reviewer_id.append(rev.find('a').get('href'))

data={"ReviewerID" :reviewer_id, "Date" :review_date, "Rating" :review_score, "Title" :review_title, "Review" :review_text}      #
data

df=pd.DataFrame(data=data)
df.index+=1

df.to_excel('realrealreviewsfromtrustpilot.xlsx')
