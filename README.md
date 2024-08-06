# How to Scrape Google Trends Data With Python

This article provides step-by-step instructions on how to get Google Trends data with Python and [SERP Scraper API](https://oxylabs.io/products/scraper-api/serp), which requires a **paid subscription** or a **free trial**.



## Why scrape Google Trends data?
Here are some of the uses for scraped Google Trends data:

- **Keyword research:** Google Trends is widely used among SEO specialists and content marketers. Since it provides insights into the past and present popularity of search terms, these professionals can tailor their marketing strategies to gain more website traffic.

- **Market research:** Google Trends data can be used for market research, helping businesses understand consumer interests and preferences over time. For example, e-commerce businesses can use Google Trends search insights for product development.

- **Societal research:** Google Trends website is a valuable resource for journalists and researchers, offering a glimpse into societal trends and public interest in specific topics.

These are just a few examples. Google Trends data can also help with investment decisions, brand reputation monitoring, and other cases.

## 1. Install libraries

For this guide, you'll need the following:
- Credentials for [SERP Scraper API](https://oxylabs.io/products/scraper-api/serp) – you can claim a **7-day free trial** by registering on the [dashboard](https://dashboard.oxylabs.io/en/);
- [Python](https://www.python.org/downloads/);
- [Requests](https://requests.readthedocs.io/en/latest/) library to make requests;
- [Pandas](https://pandas.pydata.org/docs/index.html) library to manipulate received data.

Open your terminal and run the following `pip` command:
```bash
pip install requests pandas
```

Then, import these libraries in a new Python file:

```bash
import requests, pandas
```

## 2. Send a request

Let’s begin with building an initial request to the API:

```python
import requests
from pprint import pprint

USERNAME = "YourUsername"
PASSWORD = "YourPassword"

query = "persian cat"

print(f"Getting data from Google Trends for {query} keyword..")

url = "https://realtime.oxylabs.io/v1/queries"
auth = (USERNAME, PASSWORD)

payload = {
       "source": "google_trends_explore",
       "query": query,
}

try:
    response = requests.request("POST", url, auth=auth, json=payload, timeout=180)
except requests.exceptions.RequestException as e:
    print("Caught exception while getting trend data")
    raise e

data = response.json()
content = data["results"][0]["content"]
pprint(content)
```

For more information about possible parameters, check our [documentation](https://developers.oxylabs.io/scraper-apis/serp-scraper-api/google/trends-explore).

If everything’s in order, when you run the code, you should see the raw results of the query in the terminal window like this:



