# Web Scraping With LangChain and Bright Data

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/) 

This guide explains how to combine web scraping with LangChain for real-world LLM data enrichment in this detailed step-by-step guide.

- [Using Web Scraping to Power Your LLM Applications](#using-web-scraping-to-power-your-llm-applications)
- [Benefits and Challenges of Using Scraped Data in LangChain](#benefits-and-challenges-of-using-scraped-data-in-langchain)
- [LangChain Web Scraping Powered By Bright Data: Step-by-Step Guide](#langchain-web-scraping-powered-by-bright-data-step-by-step-guide)
  - [Prerequisites](#prerequisites)
  - [Step #1: Project Setup](#step-1-project-setup)
  - [Step #2: Install the Required Libraries](#step-2-install-the-required-libraries)
  - [Step #3: Prepare Your Project](#step-3-prepare-your-project)
  - [Step #4: Configure Web Scraper API](#step-4-configure-web-scraper-api)
  - [Step #5: Use Bright Data for Web Scraping](#step-5-use-bright-data-for-web-scraping)
  - [Step #6: Get Ready to Use OpenAI Models](#step-6-get-ready-to-use-openai-models)
  - [Step #7: Generate the LLM Prompt](#step-7-generate-the-llm-prompt)
  - [Step #8: Integrate OpenAI](#step-8-integrate-openai)
  - [Step #9: Export the AI-Processed Data](#step-9-export-the-ai-processed-data)
  - [Step #10: Add Logs](#step-10-add-logs)
  - [Step #11: Put It All Together](#step-11-put-it-all-together)
- [Conclusion](#conclusion)


## Using Web Scraping to Power Your LLM Applications

Web scraping extracts data from websites to fuel RAG ([Retrieval-Augmented Generation](https://brightdata.com/blog/web-data/rag-explained)) applications and leverage LLMs ([Large Language Models](https://www.ibm.com/think/topics/large-language-models)). It bridges the gap between static databases and the real-time, domain-specific, or large datasets these applications require.

## Benefits and Challenges of Using Scraped Data in LangChain

[LangChain](https://www.langchain.com/) integrates LLMs with diverse data sources for tasks like analysis, summarization, and Q&A. However, gathering high-quality data is challenging due to anti-bot measures, CAPTCHAs, and dynamic websites. Bright Data’s [Web Scraper API](https://brightdata.com/products/web-scraper) addresses these issues with features like IP rotation, CAPTCHA solving, and JavaScript rendering, ensuring efficient and reliable data collection through simple API calls.

## LangChain Web Scraping Powered By Bright Data: Step-by-Step Guide

Learn to build a LangChain web scraping script that retrieves content from a CNN article using Bright Data’s Web Scraper API, then sends it to OpenAI for summarization. We’ll use [this CNN article](https://www.cnn.com/2024/12/16/weather/white-christmas-forecast-climate/) as our target.

![CNN article on Christmas](https://github.com/luminati-io/langchain-web-scraping/blob/main/Images/image-131-1024x492.png)

This simple example can be easily extended with additional LangChain features, such as creating an AG chatbot based on SERP data.

### Prerequisites

To get through this guide, you will need the following:

- Python 3+ installed on your machine
- An OpenAI API key
- A Bright Data account

### Step #1: Project Setup

Ensure that you have Python 3 is installed. Then create a folder for your project:

```bash
mkdir langchain_scraping
```

`langchain_scrping` will contain your Python LangChain scraping project.

Then, navigate to the project folder and initialize a Python virtual environment inside it:

```bash
cd langchain_scraping
python3 -m venv env
```

> **Note**:
>
> On Windows, use `python` instead of `python3`.

Now, open the project directory in your favorite Python IDE and add a `script.py` file inside `langchain_scraping`.

Activate the virtual environment:

```bash
./env/bin/activate
```

Or, on Windows, run:

```bash
env/Scripts/activate
```

### Step #2: Install the Required Libraries

The Python LangChain scraping project relies on the following libraries:

- [`python-dotenv`](https://pypi.org/project/python-dotenv/): To load environment environment variables from a `.env` file. It will be used to manage sensitive information like Bright Data and OpenAI credentials.
- [`requests`](https://pypi.org/project/requests/): To perform HTTP requests to interact with Bright Data’s Web Scraper API.
- [`langchain_openai`](https://pypi.org/project/langchain-openai/): LangChain integrations for OpenAI through its [`openai`](https://pypi.org/project/openai/) SDK.

In an activated virtual environment, install all the dependencies:

```bash
pip install python-dotenv requests langchain-community
```

### Step #3: Prepare Your Project

In `scripts.py`, add the following imports:

```python
from dotenv import load_dotenv
import os
```

These two lines allow you to read environment variable files.

Create a `.env` file in your project folder to store all your credentials.

Instruct `python-dotenv` to load the environment variables from `.env` in `script.py`:

```python
load_dotenv()
```

You can now read environment variables from `.env` files or the system with:

```python
os.environ.get("<ENV_NAME>")
```

### Step #4: Configure Web Scraper API

Bright Data’s Web Scraper APIs allow you to retrieve parsed content from over 100 websites easily.

To set up Web Scraper API, refer to the [official documentation](https://docs.brightdata.com/scraping-automation/web-data-apis/web-scraper-api/overview) or follow the instructions below.

Create a Bright Data account if you don't have one yet. After logging in, go to your account dashboard. Here, click on the “Web Scraper API” button on the left:

![Choosing Web Scraper API from the menu on the left](https://github.com/luminati-io/langchain-web-scraping/blob/main/Images/image-133-1024x489.png)

Since the target site is [CNN.com](http://cnn.com/), type “cnn” in the search input and select the “CNN news — Collecy by URL” scraper:

![Searching for hte CNN Scraper API](https://github.com/luminati-io/langchain-web-scraping/blob/main/Images/image-134-1024x486.png)

On the current page, click on the **Create token** button to generate a [Bright Data API token](https://docs.brightdata.com/general/account/api-token):

![Creating a new token for the API](https://github.com/luminati-io/langchain-web-scraping/blob/main/Images/image-135-1024x408.png)

This should open the following modal, where you can configure the details of your token:

![Configuring the details of the new token](https://github.com/luminati-io/langchain-web-scraping/blob/main/Images/image-136.png)

Once done, click **Save** and copy the value of your Bright Data API token.

![Once you clicked save, the new token is shown](https://github.com/luminati-io/langchain-web-scraping/blob/main/Images/image-137.png)

In your `.env` file, store this information as below:

```python
BRIGHT_DATA_API_TOKEN="<YOUR_BRIGHT_DATA_API_TOKEN>"
```

Replace `<YOUR_BRIGHT_DATA_API_TOKEN>` with the value you copied from the modal.

Your CNN news Web Scraper API page should now look similar to the example below:

![The CNN Scraper API page ](https://github.com/luminati-io/langchain-web-scraping/blob/main/Images/image-138-1024x492.png)

### Step #5: Use Bright Data for Web Scraping

The Web Scraper API starts a task tailored to your needs, then generates a snapshot of the scraped data. Here is the process overview:

1. **Submit Request:** Provide the URLs of the pages to scrape.
2. **Launch Task:** The API retrieves and parses data from the given URLs.
3. **Retrieve Snapshot:** Continuously query the snapshot API to obtain the results once the task is complete.

The POST endpoint for the CNN Web Scraper API is:

```
"https://api.brightdata.com/datasets/v3/trigger?dataset_id=gd_lycz8783197ch4wvwg&include_errors=true"
```

That endpoint accepts an array of objects containing `url` fields and returns a response like this:

```json
{"snapshot_id":"<YOUR_SNAPSHOT_ID>"}
```

Using the `snapshot_id` from this response, you then need to query the following endpoint to retrieve your data:

```
https://api.brightdata.com/datasets/v3/snapshot/<YOUR_SNAPSHOT_ID>?format=json
```

This endpoint returns HTTP status code [`202`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/202) if the task is still in progress and [`200`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/200) when the task is complete and the data is ready. The recommended approach is to poll this endpoint every 10 seconds until the task is finished.

Once the task is complete, the endpoint will return data in the following format:

```json
[
    {
        "input": {
            "url": "https://www.cnn.com/2024/12/16/weather/white-christmas-forecast-climate/",
            "keyword": ""
        },
        "id": "https://www.cnn.com/2024/12/16/weather/white-christmas-forecast-climate/index.html",
        "url": "https://www.cnn.com/2024/12/16/weather/white-christmas-forecast-climate/index.html",
        "author": "Mary Gilbert",
        "headline": "White Christmas forecast: Will you be left dreaming of snow or reveling in it?",
        "topics": [
            "weather"
        ],
        "publication_date": "2024-12-16T13:20:52.800Z",
        "updated_last": "2024-12-16T13:20:52.800Z",
        "content": "Christmas is approaching nearly as fast as Santa’s sleigh, but almost anyone in the United States fantasizing about a movie-worthy white Christmas might need to keep dreaming. Early forecasts indicate temperatures could max out around 10 to 15 degrees above normal for much of the country on Christmas Day. [omitted for brevity...]",
        "videos": null,
        "images": [
                "omitted for brevity..."
        ],
        "related_articles": [],
        "keyword": null,
        "timestamp": "2024-12-16T14:18:14.101Z"
    }
]
```

The `content` attribute contains the parsed article data, representing the information you want to access.

To implement this, first read the env from `.env` and initialize the endpoint URL constants:

```
BRIGHT_DATA_API_TOKEN = os.environ.get("BRIGHT_DATA_API_TOKEN")
BRIGHT_DATA_CNN_WEB_SCRAPER_API_URL = "https://api.brightdata.com/datasets/v3/trigger?dataset_id=gd_lycz8783197ch4wvwg&include_errors=true"
```

Now turn the above process into a reusable function:

```python
def get_scraped_data(url):
    # Authorization headers
    headers = {
    "Authorization": f"Bearer {BRIGHT_DATA_API_TOKEN}"
    }
    # Web Scraper API payload
    data = [{
        "url": url
    }]

    # Making the POST request to the Bright Data Web Scraper API
    response = requests.post(BRIGHT_DATA_CNN_WEB_SCRAPER_API_URL, headers=headers, json=data)

    if response.status_code == 200:
        response_data = response.json()
        snapshot_id = response_data.get("snapshot_id")
        if snapshot_id:
            # Iterate until the snapshot is ready
            snapshot_url = f"https://api.brightdata.com/datasets/v3/snapshot/{snapshot_id}?format=json"

            while True:
                snapshot_response = requests.get(snapshot_url, headers=headers)

                if snapshot_response.status_code == 200:
                    # Parse and return the snapshot data
                    snapshot_response_data = snapshot_response.json()
                    return snapshot_response_data[0].get("content")
                elif snapshot_response.status_code == 202:
                    print("Snapshot not ready yet. Retrying in 10 seconds...")
                    time.sleep(10)  # Wait for 10 seconds before retrying
                else:
                    print(f"Failed to retrieve snapshot. Status code: {snapshot_response.status_code}")
                    print(snapshot_response.text)
                    break
        else:
            print("Snapshot ID not found in the response")
    else:
        print(f"Error: {response.status_code}")
        print(response.text)
```

Add these two imports to make it work:

```python
import requests
import time
```

### Step #6: Get Ready to Use OpenAI Models

This example relies on OpenAI models for LLM integration within LangChain. Configure an OpenAI API key in your environment variables to use those models.

By default, `langchain_openai` automatically reads the OpenAI API key from the [`OPENAI_API_KEY`](https://python.langchain.com/docs/integrations/llms/openai/#credentials) environment variable. To set this up, add the following line to your `.env` file:

```
OPENAI_API_KEY="<YOUR_OPEN_API_KEY>"
```

Replace `<YOUR_OPENAI_API_KEY>` with the value of your [OpenAI API key](https://platform.openai.com/api-keys). If you do not know how to get one, follow the [official guide](https://platform.openai.com/docs/quickstart).

### Step #7: Generate the LLM Prompt

Define a function that takes the scraped data and produces a prompt to get a summary of the article:

```python
def create_summary_prompt(content, words=100):
    return f"""Summarize the following content in less than {words} words.

           CONTENT:
           '{content}'
           """
```

In the current example, the complete prompt will be:

```
Summarize the following content in less than 100 words.

CONTENT:
'Christmas is approaching nearly as fast as Santa’s sleigh, but almost anyone in the United States fantasizing about a movie-worthy white Christmas might need to keep dreaming. Early forecasts indicate temperatures could max out around 10 to 15 degrees above normal for much of the country on Christmas Day. It’s a forecast reminiscent of last Christmas for many, which came amid the warmest winter on record in the US. But the country could be split in two by warmth and cold in the run up to the big day. [omitted for brevity...]'
```

Here is what you will see when you pass it to ChatGPT:

![Passing the task of summarizing the content in less than 100 words](https://github.com/luminati-io/langchain-web-scraping/blob/main/Images/image-139-1024x626.png)

### Step #8: Integrate OpenAI

First, call the `get_scraped_data()` function to retrieve the content from the article page:

```python
article_url = "https://www.cnn.com/2024/12/16/weather/white-christmas-forecast-climate/"
scraped_data = get_scraped_data(article_url)
```

If the `scraped_data` is not `None`, generate the prompt:

```python
if scraped_data is not None:
    prompt = create_summary_prompt(scraped_data)
```

Finally, pass it to a [`ChatOpenAI`](https://python.langchain.com/docs/integrations/chat/openai/) LangChain object configured on the [GPT-4o mini](https://openai.com/index/gpt-4o-mini-advancing-cost-efficient-intelligence/) AI model:

```python
model = ChatOpenAI(model="gpt-4o-mini")
response = model.invoke(prompt)
```

Import `ChatOpenAI` from `langchain_openai`:

```python
from langchain_openai import ChatOpenAI
```

At the end of the process, `summary` should contain something similar to the summary produced by ChatGPT in the previous step:

```python
summary = response.content
```

### Step #9: Export the AI-Processed Data

Now you need to export the data generated by the selected AI model via LangChain to a human-readable format, such as a JSON file.

To do this, initialize a dictionary with the data you want. Then, export and then save it as a JSON file, as shown below:

```python
export_data = {
    "url": article_url,
    "summary": summary
}

file_name = "summary.json"
with open(file_name, "w") as file:
    json.dump(export_data, file, indent=4)
```

Import [`json`](https://docs.python.org/3/library/json.html) from the Python Standard Library:

```python
import json
```

### Step #10: Add Logs

The scraping process using Web Scraping AI and ChatGPT analysis may take some time. To track the script’s progress, include logs by adding `print()` statements at key steps in the script:

```python
article_url = "https://www.cnn.com/2024/12/16/weather/white-christmas-forecast-climate/"
print(f"Scraping data from '{article_url}'...")
scraped_data = get_scraped_data(article_url)

if scraped_data is not None:
    print("Data successfully scraped, creating summary prompt")
    prompt = create_summary_prompt(scraped_data)

    # Ask ChatGPT to perform the task specified in the prompt
    print("Sending prompt to ChatGPT for summarization")
    model = ChatOpenAI(model="gpt-4o-mini")
    response = model.invoke(prompt)

    # Get the AI result
    summary = response.content
    print("Received summary from ChatGPT")

    # Export the produced data to JSON
    export_data = {
        "url": article_url,
        "summary": summary
    }

    print("Exporting data to JSON")
    # Write the output dictionary to JSON file
    file_name = "summary.json"
    with open(file_name, "w") as file:
        json.dump(export_data, file, indent=4)
    print(f"Data exported to '${file_name}'")
else:
    print("Scraping failed")
```

### Step #11: Put It All Together

Your final `script.py` file should contain:

```python
from dotenv import load_dotenv
import os
import requests
import time
from langchain_openai import ChatOpenAI
import json

load_dotenv()

BRIGHT_DATA_API_TOKEN = os.environ.get("BRIGHT_DATA_API_TOKEN")
BRIGHT_DATA_CNN_WEB_SCRAPER_API_URL = "https://api.brightdata.com/datasets/v3/trigger?dataset_id=gd_lycz8783197ch4wvwg&include_errors=true"

def get_scraped_data(url):
    # Authorization headers
    headers = {
        "Authorization": f"Bearer {BRIGHT_DATA_API_TOKEN}"
    }

    # Web Scraper API payload
    data = [{
        "url": url
    }]

    # Making the POST request to the Bright Data Web Scraper API
    response = requests.post(BRIGHT_DATA_CNN_WEB_SCRAPER_API_URL, headers=headers, json=data)

    if response.status_code == 200:
        response_data = response.json()
        snapshot_id = response_data.get("snapshot_id")
        if snapshot_id:
            # Iterate until the snapshot is ready
            snapshot_url = f"https://api.brightdata.com/datasets/v3/snapshot/{snapshot_id}?format=json"

            while True:
                snapshot_response = requests.get(snapshot_url, headers=headers)

                if snapshot_response.status_code == 200:
                    # Parse and return the snapshot data
                    snapshot_response_data = snapshot_response.json()
                    return snapshot_response_data[0].get("content")
                elif snapshot_response.status_code == 202:
                    print("Snapshot not ready yet. Retrying in 10 seconds...")
                    time.sleep(10)  # Wait for 10 seconds before retrying
                else:
                    print(f"Failed to retrieve snapshot. Status code: {snapshot_response.status_code}")
                    print(snapshot_response.text)
                    break
        else:
            print("Snapshot ID not found in the response")
    else:
        print(f"Error: {response.status_code}")
        print(response.text)


def create_summary_prompt(content, words=100):
    return f"""Summarize the following content in less than {words} words.

           CONTENT:
           '{content}'
           """

# Retrieve the content from the given web page
article_url = "https://www.cnn.com/2024/12/16/weather/white-christmas-forecast-climate/"
scraped_data = get_scraped_data(article_url)

# Ask ChatGPT to perform the task specified in the prompt
prompt = create_summary_prompt(scraped_data)
model = ChatOpenAI(model="gpt-4o-mini")
response = model.invoke(prompt)

# Get the AI result
summary = response.content

# Export the produced data to JSON
export_data = {
    "url": article_url,
    "summary": summary
}

# Write dictionary to JSON file
with open("summary.json", "w") as file:
    json.dump(export_data, file, indent=4)
```

Verify that it works with this command:

```bash
python3 script.py
```

Or, on Windows:

```powershell
python script.py
```

The output in the terminal should be close to this one:

```
Scraping data from 'https://www.cnn.com/2024/12/16/weather/white-christmas-forecast-climate/'...
Snapshot not ready yet. Retrying in 10 seconds...
Data successfully scraped, creating summary prompt
Sending prompt to ChatGPT for summarization
Received summary from ChatGPT
Exporting data to JSON
Data exported to 'summary.json'
```

Open the `open.json` file that appeared in the project’s directory and you should see something like this:

```json
{
    "url": "https://www.cnn.com/2024/12/16/weather/white-christmas-forecast-climate/",
    "summary": "As Christmas approaches, forecasts indicate temperatures in the US may be 10 to 15 degrees above normal, continuing a trend from last year\u2019s warm winter. The western US will likely remain warm, while the East experiences colder conditions leading up to Christmas. Some areas may see a mix of rain and snow, but a true \"white Christmas\" requires at least an inch of snow on the ground. Historically, cities like Minneapolis and Burlington have the best chances for snow, while places like New York City and Atlanta have significantly lower probabilities."
}
```

## Conclusion

The approach faces several challenges:

- **Changing Structures:** Websites frequently update their layouts.
- **Anti-Bot Measures:** Advanced defenses are common.
- **Scalability:** Extracting large volumes of data can be complex and costly.

Bright Data’s Web Scraper API overcomes these hurdles, making it an invaluable tool for RAG and LangChain-powered solutions.

Sign up and explore our additional [offerings for AI and LLM](https://brightdata.com//use-cases/data-for-ai)!
