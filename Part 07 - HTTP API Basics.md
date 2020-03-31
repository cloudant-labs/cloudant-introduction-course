![](slides/Slide0.png)

Welcome to the Introduction to Cloudant course, an eighteen part video series that gives you an overview of the IBM Cloudant databases-as-a-service.

![](slides/Slide1.png)

---

This is part 7: "HTTP API Basics".

In the previous part we saw the  Cloudant dashboard which is a web app that is making HTTP calls to Cloudant's API. In this step we'll be using the command line to make HTTP requests and to try to add/edit/delete some documents from there. 

It's worth understanding the HTTP API from first principles even if you intend to use the higher-level client libraries.

![](slides/Slide47.png)

---

The advantage of a database having an HTTP API is that any device on the internet can read/write data if you want it to. No special software is required. No drivers speaking a custom protocol. Just an standard HTTP library. Everything speaks HTTP:

- web browser
- any programming language
- command-line scripting tools like curl
- mobile devices

We're going to learn the API using curl, a free, open-source command-line tool that can dispatch HTTP requests. curl comes pre-installed on most Macs and Unix-like operating systems. If it's not present on your computer, google "curl" and follow the installation instructions.

![](slides/Slide48.png)

---

Let's first use curl to fetch a web page - Google's homepage. 

In a command-line terminal type "curl https://www.google.com"

You should get a pageful of HTML in reply. 

If this works, then you have curl installed and you can proceed with the next tasks.

![](slides/Slide49.png)

---

Now we don't want to type the URL of our Cloudant service every time so let's save the Cloudant URL in an environment variable called URL.

`export URL=` creates a variable called URL which we can access later.

```sh
export URL="https://username:password@host"
```

Furthermore, we can create an "alias" (a shortcut) called "acurl" which saves us further typing. This "acurl" command is an alias for curl but with the JSON content-type header and a couple of useful commmand-line switches.

```sh
alias acurl="curl -sgH 'Content-type: application/json'" 
```

We can test it by fetching "acurl $URL/" and we should get some JSON back from Cloudant.

Congratulations! You've just made your first Cloudant API call.

![](slides/Slide50.png)

---

Now our `acurl` alias is set up, we can start exploring the API. Let's start with the `_all_dbs` endpoint which returns a list of databases.

Type `acurl $URL/_all_dbs` to see an array of databases.

A quick note here on formatting JSON on the command-line. We can send the output of our `acurl` command to another tool which will format the data nicely on the terminal. There are are couple of options:

- jq available from the [URL on screen](https://stedolan.github.io/jq/) which is more than just a JSON formatter - it allows JSON to be parsed, queried and manipulated too.
- or `python -m json.tool` is a simple JSON formatter, if you happen to have Python installed on your computer.


So `acurl $URL/_all_dbs | jq` means "pipe the output of acurl into jq" and what you see should be a nicely formatted, coloured output.

![](slides/Slide51.png)

---

The Cloudant API paths are hierarchical with the top level giving you information about the service and then each database sits at a level below that.

So `acurl $URL/books` gives us information about the `books` database we created earlier.

You should see information about how many documents it has, how many deleted documents and how much disk space it's occupying.

> Note: don't forget to pipe the output to jq or python to get a prettier output.

![](slides/Slide52.png)

---

If we want to see the documents contained in the database we can use the `_all_docs` endpoint.

So `acurl $URL/books/_all_docs` means get all the documents from the books database from the Cloudant service at the supplied URL.

This will return you a list of `_id` and `_rev` values for each document. If you want the document bodies too, then add `?include_docs=true` to your API call.

![](slides/Slide53.png)

---

If we want to fetch a single document back from the database, then documents sit one level below the database in the hierarchy of the URL.

So `acurl $URL/books/id` means get get document id from the database books from the Cloudant service at the supplied URL. 

Notice the hierarchy: service / database / document

![](slides/Slide54.png)

---

So far we've only used the "GET" HTTP method, which is the default one for curl and the one used when you enter a URL into your web browser.

Cloudant's API often uses the HTTP method as a "verb" to describe the action being asked of the database: GET for fetching data.

With curl we can specify the method we want to use with the `-X` command-line option.

![](slides/Slide55.png)

---

So to write a new document to our books database using the API, we're going to use the POST method, massing a document as the body of the HTTP request.

`acurl -X POST` specifies we're using the POST HTTP method. `-d` specifies the document we want to write, which is sent as the body of the request and finally the URL we are writing to which is `$URL/books` - the books database.

Alternatively we can use the `PUT` method, if we are supplying the id of the document being written. The URL becomes "$URL/books/" followed by the id we wish to write.

Both write methods yield identical response. `ok: true` to show that the write was successful. `id` being the document id written and `rev` being the revision token that was generated by the database.

![](slides/Slide56.png)

---

To modify a document we can use the PUT method, writing the new body to the URL that points to the document id we wish to overwrite. `-d` supplies the new document body and the URL not only contains the database and id of the document, but critically the `rev` - the revision of the document we intend to mutate.

If we forget and omit the `rev` parameter we will get an error response.

Note: HTTP response codes tell you whether a request succeed or not. Responses in the 200 range are successful, 400s are user errors (e.g. invalid parameters) and 500s are server-side errors. You can see the full HTTP request and response by addtionally supplying the `-v` command-line option to curl/acurl.

Also note that updates to documents happen in their entirety or not at all, there's no API construct to modify part of a document. A whole document must be supplied to overwrite a previous revision. 

![](slides/Slide57.png)

---

Finally to delete a document we use the `DELETE` method, so `-X DELETE`. We direct the request to the URL that includes the database name and document to be deleted, and critically we also supply the rev - the revision of the document to delete.

If we omit the revision token, an error is returned and the request fails.


![](slides/Slide58.png)

---

To summarise:

Understanding the HTTP API helps you grasp the relationship between your code and the Cloudant service.

The URLS are hierarchical: service/database/document or service/database/endpoint

The HTTP methods act as "verbs" defining the action to be done.

All actions can be triggered using simple HTTP API calls, from the command-line or from your code and so can be easily scripted.

![](slides/Slide59.png)

---

That's the end of this part. The next part is called ["The Bulk API"](./Part&#32;08&#32;-&#32;The&#32;Bulk&#32;API.md)
 
![](slides/Slide0.png)

---