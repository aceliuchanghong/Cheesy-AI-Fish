1. 根据title获取arxiv论文

```python
import requests
from bs4 import BeautifulSoup
import re

# URL of the search page
URL = "https://arxiv.org/search/advanced?advanced=1&terms-0-operator=AND&terms-0-term=Retrieval-Augmented&terms-0-field=title&classification-computer_science=y&classification-physics_archives=all&classification-include_cross_list=include&date-filter_by=all_dates&date-year=&date-from_date=&date-to_date=&date-date_type=submitted_date&abstracts=show&size=200&order=-announced_date_first"

# Make a request to the website and get its content
response = requests.get(URL)
soup = BeautifulSoup(response.content, 'html.parser')

# Find all the articles
articles = soup.find_all('li', class_='arxiv-result')

# Store the formatted information
results = []

for article in articles:
    title = article.find('p', class_='title is-5 mathjax').text.strip()
    date = article.find('p', class_='is-size-7').text.strip().split(';')[0].strip()
    link = article.find('p', class_='list-title is-inline-block').find('a')['href']
    
    # Extract date components using regex
    match = re.search(r"(\d{1,2}) ([A-Za-z]+), (\d{4})", date)
    day, month, year = match.groups()
    month_to_num = {
        'January': '01', 'February': '02', 'March': '03', 'April': '04', 'May': '05', 'June': '06',
        'July': '07', 'August': '08', 'September': '09', 'October': '10', 'November': '11', 'December': '12'
    }

    formatted_date = f"{year}.{month_to_num[month]}.{day.zfill(2)}"
    
    formatted_info = f"- {title}. [*{formatted_date}*] [[Arxiv]({link})]"
    results.append(formatted_info)

# Save results to a markdown file
with open('./arxiv_papers.md', 'w') as f:
    for result in results:
        f.write(result + "\n")

print("Data saved to arxiv_papers.md")
```

2. 根据摘要获取arxiv论文

```python
import requests
from bs4 import BeautifulSoup
import re

# Define the URL
url = "https://arxiv.org/search/advanced?advanced=&terms-0-operator=AND&terms-0-term=%22Retrieval-Augmented%22&terms-0-field=abstract&classification-computer_science=y&classification-physics_archives=all&classification-include_cross_list=include&date-filter_by=all_dates&date-year=&date-from_date=&date-to_date=&date-date_type=submitted_date&abstracts=show&size=200&order=-announced_date_first"

# Fetch the content
response = requests.get(url)
soup = BeautifulSoup(response.content, 'html.parser')

# Extract the required information
papers = soup.find_all('li', class_='arxiv-result')
paper_details = []

for paper in papers:
    title = paper.find('p', class_='title is-5 mathjax').text.strip()
    link = paper.find('p', class_='list-title is-inline-block').find('a')['href']
    date = paper.find('p', class_='is-size-7').text.strip().split(';')[0].strip()
    
    # Extract date components using regex
    match = re.search(r"(\d{1,2}) ([A-Za-z]+), (\d{4})", date)
    day, month, year = match.groups()
    month_to_num = {
        'January': '01', 'February': '02', 'March': '03', 'April': '04', 'May': '05', 'June': '06',
        'July': '07', 'August': '08', 'September': '09', 'October': '10', 'November': '11', 'December': '12'
    }

    formatted_date = f"{year}.{month_to_num[month]}.{day.zfill(2)}"

    paper_details.append(f"- {title}. [*{formatted_date}*] [[Arxiv]({link})]")

# Save the details to a markdown file
with open('papers.md', 'w') as f:
    f.write('\n'.join(paper_details))

print("Scraping completed and saved to papers.md!")
```

