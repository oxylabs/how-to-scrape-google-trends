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
![](images/trends_data.png)

## 3. Save results to CSV

Now that you have the results, adjust the formatting and save in the CSV format – this way, it’ll be easier to analyze the data. All this can be done with the help of the `pandas` Python library.

The response you get from the API provides you with four categories of information: `interest_over_time`, `breakdown_by_region`, `related_topics`, and `related_queries`. Let’s split each category into its own separate CSV file. 

Begin by converting each into a `pandas` dataframe:
```python
def flatten_topic_data(topics_data: List[dict]) -> List[dict]:
   """Flattens related_topic data"""
   topics_items = []
   for item in topics_data[0]["items"]:
       item_dict = {
           "mid": item["topic"]["mid"],
           "title": item["topic"]["title"],
           "type": item["topic"]["type"],
           "value": item["value"],
           "formatted_value": item["formatted_value"],
           "link": item["link"],
           "keyword": topics_data[0]["keyword"],
       }
       topics_items.append(item_dict)

   return topics_items

trend_data = json.loads(content)
print("Creating dataframes..")

   # Interest over time
iot_df = pd.DataFrame(trend_data["interest_over_time"][0]["items"])
iot_df["keyword"] = trend_data["interest_over_time"][0]["keyword"]

   # Breakdown by region
bbr_df = pd.DataFrame(trend_data["breakdown_by_region"][0]["items"])
bbr_df["keyword"] = trend_data["breakdown_by_region"][0]["keyword"]

   # Related topics
rt_data = flatten_topic_data(trend_data["related_topics"])
rt_df = pd.DataFrame(rt_data)

   # Related queries
rq_df = pd.DataFrame(trend_data["related_queries"][0]["items"])
rq_df["keyword"] = trend_data["related_queries"][0]["keyword"]
```

As the data for `related_topics` is multi-leveled, you'll have to flatten the structure into a single-leveled one. Thus, the function `flatten_topic_data` was added to do so. 

The only thing left is to save the data to a file:
```python
CSV_FILE_DIR = "./csv/"

keyword = trend_data["interest_over_time"][0]["keyword"]
   keyword_path = os.path.join(CSV_FILE_DIR, keyword)
   try:
       os.makedirs(keyword_path, exist_ok=True)
   except OSError as e:
       print("Caught exception while creating directories")
       raise e

   print("Dumping to csv..")
   iot_df.to_csv(f"{keyword_path}/interest_over_time.csv", index=False)
   bbr_df.to_csv(f"{keyword_path}/breakdown_by_region.csv", index=False)
   rt_df.to_csv(f"{keyword_path}/related_topics.csv", index=False)
   rq_df.to_csv(f"{keyword_path}/related_queries.csv", index=False)
```
You’ve now created a folder structure to hold all of your separate CSV files grouped by keyword:

![](images/trends_data_csv.png)

## 4. Create a result comparison


