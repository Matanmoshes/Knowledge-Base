# main.py

```python
import requests
from send_email import send_email

url = "https://newsapi.org/v2/everything?q=tesla&from=2024-07-03&sortBy=publishedAt&apiKey=afeac4aa570543a98f0f5f2714f023dc"

api_key = "afeac4aa570543a98f0f5f2714f023dc"

# Make a request
request = requests.get(url)

# Get a dictionary
content = request.json()

# Access the article titles and description
body= ""


for article in content["articles"]:
    title = article["title"]
    description = article["description"]
    if title and description:
        body += title + "\n" + description + 2*"\n"

body = body.encode("utf-8")
send_email(message=body)
```