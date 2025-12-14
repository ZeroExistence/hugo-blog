---
title: Selenium Python Notes
summary: Noting notes as noted
date: 2021-01-08

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Arseny Togulev on Unsplash**](https://unsplash.com/photos/white-robot-MECKPoKJYjM)'

authors:
  - me

tags:
  - Tech
  - Selenium
  - Python
---

Joting note that might be useful.

### Selenium Interaction with Svelte Link and Button
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait

def wait_until_element_found(xpath):
    WebDriverWait(browser, timeout=10).until(
            lambda x: browser.find_element_by_xpath(xpath)
        )

    # To handle scrolling just incase
    last_coordinate = 300
    while not browser.find_element_by_xpath(xpath).is_displayed():
        print(last_coordinate)
        browser.execute_script(
            "window.scrollTo(0, {0})".format(last_coordinate))
        last_coordinate += 300
        sleep(1)
    sleep(1)

    # For debugging and tracking
    element = browser.find_element_by_xpath(xpath)
    print(element.location)

    browser.find_element_by_xpath(xpath).click()

browser = webdriver.Firefox()
browser.maximize_window()
browser.get('https://tech.moe.ph/')
wait_until_element_found("//button[contains(@class,'cursor-pointer')]")
wait_until_element_found("//a[.='Tags']")
wait_until_element_found("//li[.='live-system']")
wait_until_element_found(
    "//h1[starts-with(., 'Using Persistent Live System as Poor Man')]")

# xpath with contains
# wait_until_element_found(
#     "//h1[contains(text(), 'Persistent Live System as Poor']")
# wait_until_element_found(
#     "//*[contains(text(), 'Persistent Live System as Poor']")
# Reference
# https://stackoverflow.com/questions/12323403/how-do-i-find-an-element-that-contains-specific-text-in-selenium-webdriver-pyth
# driver.find_elements_by_xpath("//*[contains(text(), 'My Button')]")

wait_until_element_found("//a[.='debian']")
wait_until_element_found(
    "//h1[starts-with(., 'Using Persistent Live System as Poor Man')]")
wait_until_element_found(
    "//h1[starts-with(., 'Using Persistent Live System as Poor Man')]")
wait_until_element_found(
    "//button[.='Back']")
browser.quit()
print("Done!")
```

### Selenium with Form Interaction
```python
from selenium import webdriver
# from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait, Select
from time import sleep

browser = webdriver.Firefox()
#browser = webdriver.Chrome()
browser.maximize_window()
# browser.get(
#     'file:///home/tonydeguia/Downloads/templated-industrious/elements.html')
browser.get(
    'https://templated.co/items/demos/industrious/elements.html')

def wait_until_element_found(xpath):
    # browser.execute_script("window.scrollTo(0, 0)")
    WebDriverWait(browser, timeout=10).until(
            lambda x: browser.find_element_by_xpath(xpath)
        )

    # To handle scrolling just incase
    # last_coordinate = 300
    # while not browser.find_element_by_xpath(xpath).is_displayed():
    #     print(last_coordinate)
    #     browser.execute_script(
    #         "window.scrollTo(0, {0})".format(last_coordinate))
    #     last_coordinate += 300
    #     sleep(1)
    sleep(1)

    # For debugging and tracking
    element = browser.find_element_by_xpath(xpath)
    print(element.location)
    # browser.execute_script("window.scrollTo({0}, {1})"
    #     .format(element.location['x'] + 20, element.location['y'] + 20))

    browser.find_element_by_xpath(xpath).click()

def wait_until_then_select(xpath, choice):
    # browser.execute_script("window.scrollTo(0, 0)")
    WebDriverWait(browser, timeout=10).until(
            lambda x: browser.find_element_by_xpath(xpath)
        )
    sleep(1)

    Select(browser.find_element_by_xpath(xpath)).select_by_value(choice)

def wait_until_then_fill(xpath, data):
    WebDriverWait(browser, timeout=10).until(
            lambda x: browser.find_element_by_xpath(xpath)
        )
    sleep(1)

    element = browser.find_element_by_xpath(xpath)
    print(element.location)

    browser.find_element_by_xpath(xpath).send_keys(data)

def wait_until_then_submit(xpath):
    WebDriverWait(browser, timeout=10).until(
            lambda x: browser.find_element_by_xpath(xpath)
        )
    sleep(1)

    element = browser.find_element_by_xpath(xpath)
    print(element.location)

    browser.find_element_by_xpath(xpath).submit()

# select by value
wait_until_then_select("//select[@id='category']", "epsilon")

# Checkbox button
# Use element instead of id for checkbox
wait_until_element_found("//label[.='Checkbox alpha']")
wait_until_element_found("//label[.='Checkbox beta']")

# Check if True
# driver.element().is_selected()

# Radio select
# Use element instead of id for radio select
wait_until_element_found("//label[.='Radio gamma']")

# Text field select
wait_until_then_fill("//input[@name='name']", "superusername")
wait_until_then_fill("//input[@name='email']", "superemailaddress")

# Submit
wait_until_then_submit("//input[@value='Submit Form']")

sleep(10)
browser.quit()
print("Done!")
```

### Selenium Modal Interaction
```python
browser.get('https://www.w3schools.com/bootstrap/bootstrap_modal.asp')
wait_until_element_found("//button[.='Click To Open Modal']")
browser.switch_to.active_element
wait_until_element_found("//p[.='Some text in the modal.']")
wait_until_element_found("//button[.='Close']")
```

### Extended Selenium Class
```python
from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait, Select
from time import sleep
import sys

class WebNavigator(webdriver.Firefox, webdriver.Chrome):
    sleep_int = 0.5
    webdriver_timeout = 10

    def __init__(self, browser):
        if browser == "Firefox":
            webdriver.Firefox.__init__(self)
        elif browser == "Chrome":
            webdriver.Chrome.__init__(self)
        else:
            sys.exit("Please specify webdriver (Firefox or Chrome).")
        self.maximize_window()

    def wait_then_click(self, xpath):
        WebDriverWait(self, timeout=self.webdriver_timeout).until(
            lambda x: self.find_element_by_xpath(xpath)
        )
        self.find_element_by_xpath(xpath).click()
        sleep(self.sleep_int)

    def wait_then_select(self, xpath, choice):
        WebDriverWait(self, timeout=self.webdriver_timeout).until(
            lambda x: self.find_element_by_xpath(xpath)
        )
        Select(self.find_element_by_xpath(xpath)).select_by_value(choice)
        sleep(self.sleep_int)

    def wait_then_fill(self, xpath, data):
        WebDriverWait(self, timeout=self.webdriver_timeout).until(
            lambda x: self.find_element_by_xpath(xpath)
        )
        self.find_element_by_xpath(xpath).send_keys(data)
        sleep(self.sleep_int)
```

### Assorted helper for Selenium
```python
import argparse

parser = argparse.ArgumentParser(description="""
    Parser
        """)
parser.add_argument("username", help="Account login")
parser.add_argument("password", help="Account password")
parser.add_argument("-b", "--browser",
                    help="Webdriver to be used. Default: Chrome")
group = parser.add_mutually_exclusive_group()
group.add_argument(
    "-a", "--all", help="Execute to all regions", action="store_true")
group.add_argument("-r", "--region", help="Execute to this specific region")
args = parser.parse_args()
print(args.username)
print(args.password)
print(args.browser)
```

### Selenium CSS Selector

[HTML TAG].[CLASS] [HTML TAG] [HTML TAG][ATTRIBUTE]

```python
wait_until_css("div.row form label[for='checkbox-alpha']")
wait_until_css("div.row form label[for='checkbox-beta']")
wait_until_css_select("div.row form #category", "epsilon")
```
