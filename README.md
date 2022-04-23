go-simpleWebServer
author: cd-stephen

# A Simple Web Application Server compiled in GO.
**note:**  Do not use this for production as it lacks proper setup error handling responces - see GO documentation on the http module.

### Diagram

![sa diagram](./img/go-simpleWebServerv2.3.svg?raw=true "Webapp Service Diagram")

### Create HTML documents
1. in the `/static` folder, create a file called `index.html`, and `form.html`.

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>staticPage</title>
</head>
<body>
    <h1>go-simpleWebServer</h1>
</body>
</html>
```


**form.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FormsWebsite</title>
</head>
<body>
    <div>
        <form method="POST" action="/form">
        <label>First Name</label><input name="firstName" type="text" value=""/>
        <label>Last Name</label><input name ="lastName" type="text" value=""/>
        <input type="submit" value="submit"/>
</body>
</html>
```

**main.go**
- set the `package` to `main` - as there will be only one package set to become executable with a entrypoint of `func main()`
-  import `fmt`, `log`, and `net/http`
```go
package main

import (
	"fmt"
	"log"
	"net/http"
)
```

### Set Function Entry Point


```go
func main() {
}

```

### Setup the simple static webserver
- we define a variable object called `fileServer` and assign (short form assignment `:=`) it to the imported module function `http.FileServer`  passing the parameters of the target directory hosting the `index.html` file - this is the default file name this **go** funtion will look for.
- https://pkg.go.dev/net/http@go1.18.1#FileServer

```go
func main() {
	fileServer := http.FileServer(http.Dir("./static"))
}

```

-create a handle function for the / (document root) route and send it to the `fileServer` object defined
```
http.Handle("/", fileServer) or 
http.Handle("/", http.FileServer(http.Dir("./static")))
```

-define a route to a function - https://pkg.go.dev/net/http@go1.18.1#HandleFunc
```go
http.HandleFunc("/route", functionName)
ie.
http.HandleFunc("/form", formHandler)
http.HandleFunc("/function", functionHandler)

```

- the ListenAndServer function in the html package is the actual server that listens for TCP requests and log on error - https://pkg.go.dev/net/http@go1.18.1#Server.ListenAndServe
```go
fmt.Printf("Starting server at port 8080\n")
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatal(err)

```


### Setup the functions
- define the function passing in the `w http.ResponseWriter` and `r *http.Request` parameters - with any route, you will have a `responce` and a `request`.  the `request` is something that the user send to the server and the `responce` is what the server send back.  The `*` in `*http.Request` is a **pointer** for `r` - so we can say that `r` is pointing to the `request` - understand pointers in golang
```
func functionHandler(w http.ResponseWriter, r *http.Request)
```

- Setup some basic error handling against the inbound `URL` path in the http request (`r`), and make sure the html **method** is not anything other then `GET` - this specific function is not equiped to handle a html `POST` - `http.StatusNotFound` is in the go http package as a `const` that is set to `404`
```go
if r.URL.Path != "/function" {
		http.Error(w, "404 not found", http.StatusNotFound)
		return
	}
	if r.Method != "GET" {
		http.Error(w, "This is not a supported METHOD of this function", http.StatusNotFound)
		return
	}
```
