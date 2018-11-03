# Headless Selenium

## 1. Install Selenium 
```shell
pip install -U selenium
```

## 2. Download chromedriver
You need a browser driver to run the selenium. Download Chrome driver from `https://sites.google.com/a/chromium.org/chromedriver/downloads`. This will enable Selenium to run all the test cases with browser. The following code demonstrates the usage :
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

browser = webdriver.Chrome(<Path of the executable chromedriver>)

browser.get('http://www.google.com')
print(browser.title)

browser.close()
```
##  3. Headless Selenium
The above solution opens a browser in the GUI format. Suppose we do not want the browser to be displayed (say we are running Selenium on a server without linuX GUI), but we still want the tests to occur in the background, we can modify the above code slightly. Add a `"headless"` argument to `ChromeOptions()` instead of just using `Chrome()`. The below code executes the same functionality as the one above, without displaying the browser (Runs the selenium script as a chrome background activity without opening the actual GUI browser) :
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

options = webdriver.ChromeOptions()
options.add_argument('headless')
browser = webdriver.Chrome(<Path of the executable chromedriver>, options = options)

browser.get("http://www.google.com")
print(browser.title)

browser.close()
```
