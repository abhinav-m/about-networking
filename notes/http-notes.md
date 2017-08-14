# About HTTP

The *Hypertext transfer protocol* is the mechanism through which data is requested and provided on the web.


## Understanding the protocol

When you type an address in your browser's address bar, the browser looks up the address of the server associated with the request and tries to open a **TCP** connection to it on **port 80** , default port for HTTP.

If the server accepts the connection (and exists) , browser sends something like :


A simple request example using curl :

```
//Request
GET / HTTP/1.1
> User-Agent: curl/7.35.0
> Host: www.google.co.in
> Accept: */*

//Response
< HTTP/1.1 200 OK
< Date: Mon, 14 Aug 2017 07:10:35 GMT
< Expires: -1
< Cache-Control: private, max-age=0
< Content-Type: text/html; charset=ISO-8859-1
< ...  >

<!DOCTYPE html>
 ...the rest of the document
 
```

The browser then takes the part of the response after the blank line and displays it as an HTML document.

### Request
The information sent by the client is called the request. It starts with this line:
`GET / HTTP/1.1`

The first word is the method of the request. 

**GET** means that we want to get the specified resource.

 Other common methods are **DELETE** to delete a resource, **PUT** to replace it, and **POST** to send information to it. Note that the server is not obliged to carry out every request it gets. If you walk up to a random website and tell it to DELETE its main page, it’ll probably refuse.


The part after the method name is the path of the resource the request applies to. In the simplest case, a resource is simply a file on the server, but the protocol doesn’t require it to be.

A resource may be anything that can be transferred as if it is a file. Many servers generate the responses they produce on the fly. For example, if you open *twitter/ marijnjh*, the server looks in its database for a user named *marijnjh*, and if it finds one, it will generate a profile page for that user.

After the resource path, the first line of the request mentions HTTP/1.1 to indicate the version of the HTTP protocol it is using.


### Response

The server’s response will start with a version as well, followed by the status of the response, first as a three-digit status code and then as a human-readable string.

`HTTP /1.1 200 OK`

* Status codes starting with a **2** indicate that the request succeeded. 
* Codes starting with **4** mean there was something wrong with the request. `404` is probably the most famous HTTP status code—it means that the resource that was requested could not be found.
*  Codes that start with **5** mean an error happened on the server and the request is not to blame.


### Headers

The first line of a request or response may be followed by any number of headers. These are lines in the form “name: value” that specify extra information about the request or response. These headers were part of the example **response**:

```
 Date: Mon, 14 Aug 2017 07:10:35 GMT
< Expires: -1
< Cache-Control: private, max-age=0
< Content - Length : 65585
< Content-Type: text/html; charset=ISO-8859-1

```
This tells us the size and type of the response document. In this case, it is an **HTML document of 65,585 bytes**. It also tells us when that document was last modified.


For the most part, a client or server decides which headers to include in a request or response, though a few headers are required. 

For example, the **Host** header, which specifies the hostname, should be included in a request because a server might be serving multiple hostnames on a single IP address, and without that header, the server won’t know which host the client is trying to talk to.
`> Host: www.google.co.in`

After the headers, both requests and responses may include a blank line followed by a body, which contains the data being sent. 

**GET** and **DELETE** requests don’t send along any data, but **PUT** and **POST** requests do.

Similarly, some response types, such as error responses, do not require a body.

## Browsers and HTTP

As we saw in the example, a browser will make a request when we enter a URL in its address bar. When the resulting HTML page references other files, such as images and JavaScript files, those are also fetched.

A moderately complicated website can easily include anywhere from 10 to 200 resources. To be able to fetch those quickly, browsers will make several requests simultaneously, rather than waiting for the responses one at a time. 

Such documents are always fetched using **GET** requests.

HTML pages may include forms, which allow the user to fill out information and send it to the server. This is an example of a form:
```javascript
< form method =" GET " action =" example / message . html ">
<p > Name : < input type =" text " name =" name " > </p >
<p > Message : <br > < textarea name =" message " > </ textarea > </p >
<p >< button type =" submit " > Send </ button > </p >
</ form >
```

This code describes a form with two fields: a small one asking for a name and a larger one to write a message in. When you click the Send button, the information in those fields will be encoded into a query string.When the `<form> `element’s method attribute is **GET** (or is omitted), that query string is tacked onto the action URL, and the browser makes a **GET** request to that **URL** .

`GET / example / message . html ? name = Jean & message = Yes %3 F HTTP /1.1`

The start of a query string is indicated by a question mark. After that follow pairs of names and values, corresponding to the name attribute on
the form field elements and the content of those elements, respectively. 

An ampersand character **( & )** is used to separate the pairs. The actual **message** encoded in the previous URL is **“Yes?”**, even though the question mark is replaced by a strange code. 

Some characters in query strings must be escaped. The question mark, represented as `%3F `, is one of those. There seems to be an unwritten rule that every format needs its own way of escaping characters.

 This one, called **URL encoding**, uses a percent sign followed by two hexadecimal digits that encode the character code. In this case, **3F**, which is **63** in decimal notation, is the code of a question mark character.
 
  JavaScript provides the **encodeURIComponent and decodeURIComponent** functions to encode and decodethis format.
  
```
console . log ( encodeURIComponent (" Hello & goodbye ") );
// → Hello %20%26%20 goodbye
console . log ( decodeURIComponent (" Hello %20%26%20 goodbye ") );
// → Hello & goodbye
```
If we change the method attribute of the **HTML** form in the example we saw earlier to **POST ** , the  **HTTP ** request made to submit the form will use the  **POST ** method and **put the query string in body of the request, rather than adding it to the URL.**
```
POST / example / message . html HTTP /1.1
Content - length : 24
Content - type : application /x - www - form - urlencoded
name = Jean & message = Yes %3 F
```
By convention, the  **GET**  method is used for requests that do not have side effects, such as doing a search. Requests that change something on the server, such as creating a new account or posting a message, should be expressed with other methods, such as   **POST**  . 

Client-side software, such as a browser, knows that it shouldn’t blindly make  **POST**  requests but will often implicitly make   **GET**  requests—for example, to prefetch a resource it believes the user will soon need.
