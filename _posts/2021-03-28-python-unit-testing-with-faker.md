---
layout: post
title:  "Python Unit Testing with Faker"
category: python
tags: python testing
published: true
---

![fakeit](/assets/fake-it-til-you-make-it.jpeg?style=centerme)

Faker is a python package that can generate random attributes that I find useful for unit testing as we'll see in a moment. This is not to be confused with unit test fakes which is a lightweight and limited implementation of a more complex counterpart. 

# Virtual Environment Setup

Before we being, create a project directory and set up a python virtual environment. Out of habit, I upgrade the pip, setuptools, and wheel packages to avoid pip installation issues.

{% highlight bash %}
$ mkdir faker-test
$ cd faker-test
$ python3.8 -m venv --prompt faker venv
$ source venv/bin/activate
(faker) $ python -m pip install --upgrade pip setuptools wheel
{% endhighlight %}

# Install Faker

Once your environment is set up, install faker using `python -m pip install faker`. This is what my virtual environment looks like at this point.

{% highlight bash %}
(faker) $ python -m pip list
Package         Version
--------------- -------
Faker           6.6.3
pip             21.0.1
pkg-resources   0.0.0
python-dateutil 2.8.1
setuptools      54.2.0
six             1.15.0
text-unidecode  1.3
wheel           0.36.2
{% endhighlight %}

# Faker Basics

From the REPL, we can explore a few of faker's offerings. See the [Faker Docs](https://faker.readthedocs.io/en/master/index.html) for more details as they are vast.

{% highlight bash %}
(faker) $ python
>>> import faker
>>> fake = faker.Faker()
>>> fake.user_name()
'joshuaedwards'
>>> fake.pydict()
{'use': 'http://johnson.info/privacy/', 'among': datetime.datetime(2003, 10, 14, 9, 21, 3), 'raise': 'gonzaleschristian@morgan.info', 'sell': 'swoods@gmail.com', 'evening': 'uuLEVbLchYSbNMtgjaDI', 'stuff': 5151704.9, 'trial': 'UfmwdDpeYEfRCtdnEtdJ'}
>>> fake.credit_card_number()
'30568859585168'
{% endhighlight %}

# Example User Class

Let's make a very simple user class and save in `user.py` in your project folder.

{% highlight python %}
from dataclasses import dataclass

@dataclass
class User():
    user_name: str
    first_name: str
    last_name: str
{% endhighlight %}

# Unit Tests

Create a `test_user.py` file in the project directory and copy/paste the code below. The code creates a test class with one unit test function to compare the user name attribute.

{% highlight python %}
import unittest
from user import User

class testUser(unittest.TestCase):

    def test_user_name(self):
        usr = User('jdoe', 'John', 'Doe', 'President')
        self.assertEqual(usr.user_name, 'jdoe')
{% endhighlight %}

To run the unit test, run the following command. 

{% highlight bash %}
(faker) $ python -m unittest
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
{% endhighlight %}

Our single test passed but there are a couple of problems with it. For one, we are violating the [DRY (Don't Repeat Yourself)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) principle by having the 'jdoe' user name string in two places. We can get around that by making a user_name variable and the repeating this approach for each of the other attributes.

{% highlight python %}
import unittest
from user import User

class testUser(unittest.TestCase):

    def test_user_name(self):
        user_name = 'jdoe'
        usr = User(user_name, 'John', 'Doe', 'President')
        self.assertEqual(usr.user_name, user_name)
{% endhighlight %}

The second problem with our current test is that we will only ever test against this one fictional John Doe user. This is where faker can help.

# Unit Tests with Faker

Using the faker package, we can get some much needed variability for our User class properties. Each time a faker function is called, it generates a fresh value.

{% highlight bash %}
(faker) $ python
>>> from faker import Faker
>>> fake = Faker()
>>> for _ in range(10):
...    print(fake.first_name())
Brent
Thomas
Devin
Martha
Katie
Aaron
Kristina
Tracy
Anthony
Annette
{% endhighlight %}

Now let's extend our original unit test class to use faker. When the unit test is run, the `setUpClass` function creates a new User. This User will have different values each time the test is run and we aren't violating DRY since we store the fake value and compare with the user attribute in our assertions. I would encourage you to look through the various faker providers to see if anything would be useful for your software and testing practice.

{% highlight python %}
import unittest
from faker import Faker
from user import User

class UserTest(unittest.TestCase):

    @classmethod
    def setUpClass(cls):
        fake = Faker()
        cls.user_name = fake.user_name()
        cls.first_name = fake.first_name()
        cls.last_name = fake.last_name()
        cls.user = User(
                user_name = cls.user_name,
                first_name = cls.first_name,
                last_name = cls.last_name
                )

    def test_user_name(self):
        self.assertEqual(self.user.user_name, self.user_name)

    def test_first_name(self):
        self.assertEqual(self.user.first_name, self.first_name)

    def test_last_name(self):
        self.assertEqual(self.user.last_name, self.last_name)
{% endhighlight %}
