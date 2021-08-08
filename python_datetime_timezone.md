---
title: "Get Current Time with Time Zone Info in Python"
date: 2020-04-17 21:54:48+0800
tags: []
categories: [Python]
---

I am trying to generate a custom time format using the Python `datetime`
package. My original code is:

```python
from datetime import datetime

print(datetime.now().strftime("%Y-%m-%d %H:%M:%S%z"))
```

<!--more-->

Strangely, the time zone field `%z` is not present in the generated time
string. The generated string is like:

> '2020-04-17 20:41:35'

I checked the [documentation of `strftime()`](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-format-codes),
and the meaning of `%z` is:

> UTC offset in the form `±HHMM[SS[.ffffff]]` (empty string if the object is naive).

Here, we need to understand what is a `naive` or `aware` time object.  Simply
put, a `naive` time object has no timezone info, thus is incomplete. On other
hand, an `aware`time object has timezone info and is `complete`. A full
explanation can be found
[here](https://docs.python.org/3/library/datetime.html#aware-and-naive-objects).


By default, the time object we get from `datetime.now()` is a naive object,
thus having no timezone info attached. To convert the time object to an aware
object with our local time zone info, we can use
[`datetime.astimezone()`](https://docs.python.org/3/library/datetime.html#datetime.datetime.astimezone):

```python
aware_time = datetime.now().astimezone()
print(aware_time.strftime("%Y-%m-%d %H:%M:%S%z"))
```

Now, we will get the expected result:

> 2020-04-17 20:53:13+0800

# References

+ [`%Z` in `strftime()` does not work ](https://stackoverflow.com/q/31299580/6064933).
+ [How to make a naive time object an aware time object](https://stackoverflow.com/q/7065164/6064933).
+ [What is the point of a naive datetime?](https://stackoverflow.com/q/24281525/6064933)
