---
layout: post
title:  "Python Package Version Search"
category: python
tags: python
published: true
---

![search](/assets/hiker-silhouette-mountain-top-984083.jpg)

One of the more useful features I have leveraged in Python is the ability to search known package versions without having to leave the terminal. Since I frequently ssh into remote machines (AWS EC2 instances and internal company servers), this feature allows me to remain on task.

# Python Package Installation

Installing a specific version of a package is accomplished by naming the package (tensorflow in this case) and the version (2.4.1) as seen below.

{% highlight bash %}
(venv) $ python -m pip install tensorflow==2.4.1
{% endhighlight %}

# Package Version Search Trick

By removing the version in the installation command, pip will return an installation error but also helpfully return a list of possible package versions.

{% highlight bash %}
(venv) $ python -m pip install tensorflow==
ERROR: Could not find a version that satisfies the requirement tensorflow== (from versions: 2.2.0rc1, 2.2.0rc2, 2.2.0rc3, 2.2.0rc4, 2.2.0, 2.2.1, 2.2.2, 2.3.0rc0, 2.3.0rc1, 2.3.0rc2, 2.3.0, 2.3.1, 2.3.2, 2.4.0rc0, 2.4.0rc1, 2.4.0rc2, 2.4.0rc3, 2.4.0rc4, 2.4.0, 2.4.1)
ERROR: No matching distribution found for tensorflow==
{% endhighlight %}

This helpful list of possible packages can remind the developer which versions are available; that is up until pip 20.3.

# Virtual Environment Setup

Now going through my standard virtual environment setup yields a new pip version. The latest being 21.0.1.

{% highlight bash %}
$ python3.8 -m venv venv
$ source venv/bin/activate
(venv) $ python -m pip install --upgrade pip
(venv) $ python -m pip --version
pip 21.0.1 
{% endhighlight %}

If I try the package installation trick from above, I get an error because this version of pip is using the new resolver.

{% highlight bash %}
(venv) $ python -m pip install tensorflow==
ERROR: Could not find a version that satisfies the requirement tensorflow==
ERROR: No matching distribution found for tensorflow==
{% endhighlight %}

# Legacy Resolver

The folks at PyPA (Python Packaging Authority) are working out issues with inconsistent package dependency management. For now, you can add the '--use-deprecated' option to retain the former dependency resolver to get the same useful behavior described previously.

{% highlight bash %}
(venv) $ python -m pip install --use-deprecated=legacy-resolver tensorflow==
ERROR: Could not find a version that satisfies the requirement tensorflow== (from versions: 2.2.0rc1, 2.2.0rc2, 2.2.0rc3, 2.2.0rc4, 2.2.0, 2.2.1, 2.2.2, 2.3.0rc0, 2.3.0rc1, 2.3.0rc2, 2.3.0, 2.3.1, 2.3.2, 2.4.0rc0, 2.4.0rc1, 2.4.0rc2, 2.4.0rc3, 2.4.0rc4, 2.4.0, 2.4.1)
ERROR: No matching distribution found for tensorflow==
{% endhighlight %}

The legacy resolver was supposed to be removed in the January 2021 release of pip 21.0 but it is thankfully still working.

> You can use the deprecated (old) resolver, using the flag --use-deprecated=legacy-resolver, until we remove it in the pip 21.0 release in January 2021. No word on a new deadline yet.

References:

[https://discuss.python.org/t/announcement-pip-20-3-release/5948](https://discuss.python.org/t/announcement-pip-20-3-release/5948)
