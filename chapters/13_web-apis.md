<!--
---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Julia
  language: julia
  name: julia-1.9
---
-->

Surfing the Web
===============

The **hypertext transfer protocol** (HTTP) is a set of rules for communication
between computers on a network. HTTP has a request-response model:

1. The **client** sends a **request** to a **server**. For example, the client
   might request a file.
2. The server sends a **response** to the request.

Your web browser goes through this process every time you visit a website. HTTP
requests are also one way to download data sets from the web.

There are several different HTTP request methods:

* **GET** requests data from the server.
    + Example: Visiting a web site
    + You can send a small amount of data with a GET request through the URL
* **POST** sends data to the server.
    + Example: Submitting a web form
    + The response to a POST request often contains data too
* [And many more...][http-requests]

[http-requests]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods

:::{tip}
If you browse the web with Chrome or Firefox, you can inspect the HTTP requests
your brower makes when you visit a website. Go to a website and press
`Ctrl-Shift-i` (OS X: `Cmd-Shift-i`) to open your browser's web developer
tools. Click on the "Network" tab and reload the website. You should see the
tab populate with all of the requests your browser makes as it loads the
website.
:::

After you make an HTTP request, the server will send a response. Every HTTP
response includes a 3-digit status code to summarize the meaning of the
response:

Range            | Meaning                              | Name
---------------- | ------------------------------------ | ----
100s (100 - 199) | Request received, still processing   | Informational
200s             | Success                              | Success
300s             | The requested data is somewhere else | Redirect
400s             | Something's wrong with the request   | Client Error
500s             | Something's wrong with the server    | Server Error

You can find a list of common status codes in [Mozilla's developer
documentation][http-status].

[http-status]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status

A response usually also contains **content** as text or raw bytes. Some common
formats for the content are:

* Hypertext Markup Language (HTML)
* Extensible Markup Language (XML)
* JavaScript Object Notation (JSON)

### The requests Package

```{code-cell}
:tags: [remove-cell]
import requests
import requests_cache

requests_cache.install_cache("day4_http_cache")
```

The **requests** package provides functions for making HTTP requests from
Python. You can use requests to make a GET request with the `get` function:

```{code-cell}
import requests

response = requests.get("https://datalab.ucdavis.edu")
```

The status code of the response is in the `.status_code` attribute:

```{code-cell}
response.status_code
```

If you want Python to raise an error any time the status code indicates a
problem, you can use the `.raise_for_status` method. It doesn't do anything if
the status code is okay:

```{code-cell}
response.raise_for_status()
```

The content of the response is stored as bytes in the `.contents` attribute and
as text in the `.text` attribute. Choose which one to use based on what kind of
site or file you requested. Web pages (`.html`) are usually text files, so for
the DataLab site it makes more sense to use `.text` to access the content:

```{code-cell}
text = response.text

# Only display the first 200 characters
print(text[:200])
```

(request-etiquette)=
### Request Etiquette

Making an HTTP request is not free. It has a real cost in CPU time and
electricity. Server administrators will not appreciate it if you make too many
requests or make requests too quickly. So:

* Use the `time.sleep` function to slow down any requests you make in a loop
  ({numref}`loops` explains loops). Aim for no more than 20-30 requests per
  second.
* Install and use the __requests_cache__ package to avoid downloading extra
  data when you make the same request twice.

Failing to be polite can get you banned from websites!

```
# In a Terminal (not Python console!):
#   conda install -c conda-forge requests-cache
#

import requests_cache

requests_cache.install_cache("my_cache")
```


### Web APIs

An **application programming interface** (API) is a collection of functions and
data structures for communicating with other software. For instance, whenever
you use a Python package, you're using the API created by the package's
developers.

Websites sometimes provide a *web* API so that programmers can create
applications that access data and settings. The most common kind of web API is
**representational state transfer** (REST). The key ideas of REST are that:

* You can "call" functions by making HTTP requests.
* URLs called **endpoints** serve as function names.
* Separate function calls are handled independently of each other. The server
  doesn't remember previous calls.

For example, the [Star Wars API](https://swapi.dev/) (SWAPI) is a REST API with
endpoints that return data about the Star Wars universe. The best way to learn
how to use a web API is to read its documentation, and fortunately the Star
Wars API is [well-documented][swapi-docs].

[swapi-docs]: https://swapi.dev/documentation


Try making a request to SWAPI's "people" endpoint (the endpoint URL comes from
the SWAPI documentation):

```{code-cell}
url = "https://swapi.dev/api/people/1/"
response = requests.get(url)
```

Don't forget to check the status of the response:

```{code-cell}
response.status_code
```

A 200 status code means the request was successful, so the next step is to take
a look at the content of the response.

Unless you've already read through the SWAPI documentation, you probably don't
know whether the content will be bytes or text. When you're unsure, you can try
printing the `.text` value to check whether the content looks like text. Byte
content will usually look like gibberish if you try to display it as text.
Here's the content of the response:

```{code-cell}
response.text
```

This is text about Luke Skywalker. In fact, according to the SWAPI
documentation, all of the SWAPI endpoints return text.

Although the response content is text, it isn't ordinary English text. It's
full of curly braces, square brackets, quotes, and other punctuation. If you
think back to the beginning ({numref}`containers`) of this chapter, the way the
punctuation is arranged might start to look familiar...


### JavaScript Object Notation

The response content looks a lot like Python lists and dictionaries! The
response is not actually Python code, but it *is* JavaScript code. In
JavaScript, lists and dictionaries are written the same way as in Python. The
response is in a popular non-tabular data format called **JavaScript Object
Notation** (JSON).

JavaScript Object Notation (JSON) looks and works a lot like Python lists and
dictionaries. Lists are surrounded with `[ ]`, and dictionaries are surrounded
with `{ }`. There are many Python packages for converting JSON text into Python
lists and dictionaries.

:::{tip}
You can use Python's built-in [json][] module to read a JSON file you didn't
get from an HTTP request. For example, Jupyter notebooks (`.ipynb` files) are
in JSON format.
:::

When you know the content of a response is in JSON format, you can use the
`.json` method on the response to convert it into Python lists and
dictionaries:

```{code-cell}
luke = response.json()

luke
```

```{code-cell}
type(luke)
```

[json]: https://docs.python.org/3/library/json.html

In this case, the outer object is a dictionary. The keys in the dictionary are:

```{code-cell}
list(luke.keys())
```

These keys describe the included information about Luke. For example, to get
his eye color, you can use indexing:

```{code-cell}
luke["eye_color"]
```

Sometimes data in JSON format will contain several layers of dictionaries and
lists. Remember that you can use indexing with square brackets `[ ]` to
navigate these.

### Using Endpoints

The SWAPI endpoint `/people/` returns information in JSON format about
different people in the Star Wars universe. An ID number after `/people/`
determines the person for which the endpoint returns information. Think of the
ID number as an argument to the endpoint. For instance, Luke Skywalker's ID
number is `1`, so the URL for information about Luke is:

```text
https://swapi.dev/api/people/1/
```

A good way to work with an endpoint that accepts arguments is to use Python to
paste the arguments onto the end. For example, this code pastes the number `2`
onto the end of the string `endpoint`:

```{code-cell}
endpoint = "https://swapi.dev/api/people/"
endpoint + str(2)
```

The `str` function converts the ID number into a string, and `+` pastes the two
strings together.

Now try getting the information for the person with ID number `2`:

```{code-cell}
response = requests.get(endpoint + str(2))

# Raise an error if the request failed
response.raise_for_status()

person = response.json()
person
```

So the "person" with ID number `2` is actually the android C3-PO.

You can make using the endpoint more convenient by writing a Python function
that takes an ID number as input, makes a request to the endpoint, and then
returns the result. Here's the code:

```{code-cell}
def get_swapi_person(id_num):
    endpoint = "https://swapi.dev/api/people/"

    # Note that 'id_num' replaces '2'
    response = requests.get(endpoint + str(id_num))

    # Raise an error if the request failed
    response.raise_for_status()

    return response.json()
```

As always, when you write a function, it's important to test it out. For
example:

```{code-cell}
get_swapi_person(50)
```

When you're working with a web API, it's generally a good idea to create
**wrapper functions**, like `get_swapi_person`, that turn each endpoint into an
actual Python function by wrapping up (or encapsulating) the requests code.
