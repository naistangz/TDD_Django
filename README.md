# Test Driven Development with Django

**Contents**
- [x][Why TDD?](#why-tdd)
- [x][TDD Philosophy](#tdd-philosophy)
- [x][Functional Tests](#using-djangos-test-suite-for-functional-testing)
- [x][Unit Tests](#writing-unit-tests)
- [x][Ajax Testing](#ajax-testing)
- [x][Deployment Testing](#deployment-testing)
- [x][Edge cases TDD](#edge-cases-for-tdd)

---

## Why TDD?
- Gives confidence to make changes to the software
- Allows more agility for other developers who are not familiar with the details of the software to confidently modify the source code without introducing errors
- Reduce bugs 
- Ensure comprehensive test coverage of the software, so bugs are more likely noticed in the test case
- Allows developers to work out potential issues before the application is ready to go into production

## TDD Philosophy
- Write a failing test
- Write code to pass the test 

## Functional vs Unit Tests
Functional | Unit
---|---
Functionality|Code
Make sure users can do things with the website|Make sure that a small piece of the project is working as it should
Something that users would know and care about e.g ability to click on a button and navigating to a link successfully|Something that user would have no idea about e.g creating an object from a class or functions

## Setting up a Django project 
Installing virtual environment with pip (python package manager):
```bash
pip3 install virtualenv
```
Creating a virtual environment:
```bash
virtualenv venv
```
Activating the virtual environment:
```bash
source venv/bin/activate
```
Installing selenium to test browsers:
```bash
pip install selenium
```

Installing GeckoDriver :lizard:
```bash
https://github.com/mozilla/geckodriver/releases
```
Moving GeckoDriver to `/venv/bin`
```bash
mv ~/Downloads/geckodriver ~/Desktop/venv/bin
```
Creating a python file to test browser:
```bash
nano functional_tests.py
```
Creating an initial test that automatically fails
> *Source code* [HERE](functional_tests.py)
```python
# functional_tests.py
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('http://localhost:8000')

assert browser.page_source.find('install')
```
Running the test:
```bash
python functional_tests.py
```
This will fail because we do not have any browsers running on port 8000 (default Django port)

## Using Django's test suite for functional testing
Installing Django
```bas
pip install django
```
Creating a new django project:
```bash
(venv) ➜  TDD_DJANGO git:(master) ✗ django-admin startproject hashthat
```
Navigate to new project:
```bash
cd hashthat/
```
Running the Django server:
```bash
python manage.py runserver
```
Rerunning the test (open in new terminal):
```bash
cd TDD_Django/
python functional_tests.py
```
Creating a Django app directory:
```bash
cd hashthat
(venv) ➜  hashthat git:(master) ✗ django-admin startapp hashing
```
Which gives the following folder structure:
```bash
# hashing/
.
├── __init__.py
├── admin.py
├── apps.py
├── migrations
│   └── __init__.py
├── models.py
├── tests.py
└── views.py
```
The initial test that was created will go into the `tests.py` file:
```python
# tests.py
from django.test import TestCase
from selenium import webdriver

class FunctionalTestCase(TestCase):

    def setUp(self):
        self.browser = webdriver.Chrome()
        # self.browser required in order to become a property of the class

    def test_there_is_homepage(self):
        self.browser.get('http://localhost:8000')
        self.assertIn('install', self.browser.page_source)

    def tearDown(self):
        self.browser.quit()
```

Running the test:
```bash
cd TDD_Django/hashthat/
python manage.py test
```
Which returns:
```bash
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 5.079s

OK
Destroying test database for alias 'default'...
```

## Writing Unit Tests
- Testing individual modules of an application (e.g field length for models)
```python
# tests.py
    # validating models to ensure that hashed string is exactly 64 characters long
    def test_bad_data(self):
        def badHash():
            hash = Hash()
            hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824sdafdsfadsfadsfasdf'
            hash.full_clean() # to check invalid value for IntegerField
        self.assertRaises(ValidationError, badHash)
```

<img src="https://cdn.softwaretestinghelp.com/wp-content/qa/uploads/2016/12/image-result-for-unit-testing-vs-functional-testin.png">


## Ajax Testing
- Asynchronous Javascript and XML
- Used on client-side (browser) to create interactive Web applications 
- With AJAX, web apps can retrieve data from the server asynchronously in the background without interfering with the display and behaviour of the existing page.
- This means web pages can be updated asynchronously by exchanging small amounts of data with the server behind the scenes, without having to reload the whole page. 

Synchronous Communication:
<img="https://www.microfocus.com/documentation/silk-performer/205/en/silkperformer-205-webhelp-en/GUID-17EF1B10-8A5A-4511-A429-50B9E6A9F93A-low.png" alt="synchronous_communication">

Asynchronous Communication:
<img src="https://www.microfocus.com/documentation/silk-performer/205/en/silkperformer-205-webhelp-en/GUID-0C4CDA57-CE7E-4F82-B227-8E8E6782509D-low.png">

**Benefits**
- Web applications request only the select content that is needed to update the page, thereby reducing bandwidth usage and load time.

## Deployment Testing 
- Once tests have passed successfully on local server, we want to move on to deploying code to a staging server (not live production server)
- We want to have a separate server that has its own database
- It is recommended to have a custom domain so that we can always get to the staging server when deploying new code in order to run functional tests locally against the staging site to see what it would be like to be a user accessing the staging website and on that staging server, we would run the unit tests to make sure everything is working exactly as it should.
- In staging server, we want to run our unit tests to make sure everything is working exactly as it should e.g connections with the database, etc
- Once test in staging area have passed, we deploy changes to production where we can run functional tests again on the production.
- We want to **automate** this process so every time we make a change to the website, tests are run automatically 

## Edge cases for TDD
- When a project is young, it changes too much for testing
- You'll often delete features and the tests associated with them 
- Wait until you have version 1.0 of product
- Only create tests for things you find crucial 
- Provide freedom and security that code is working 