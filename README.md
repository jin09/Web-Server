# Building A Web Server From Scratch

The reason why this is important is because a mechanic must understand how a car works. 
Only then will he be able to fix things when things go wrong.  

### What is a Web Server?

In a nutshell it’s a networking server that sits on a physical server (server on a server, NSFW) and waits for a client to send a request. 
When it receives a request, it generates a response and sends it back to the client. 
The communication between a client and a server happens using HTTP protocol. 
A client can be your browser or any other software that speaks HTTP.

![alt text](https://ruslanspivak.com/lsbaws-part1/LSBAWS_HTTP_request_response.png "Client Server")

### Socket Programming

A TCP connection is first established between the client and the server.
Then the client sends some raw text (HTTP Spec) over the connections, the server makes sense of it,
and responds accordingly.

```bash
$ telnet localhost 8888
Trying 127.0.0.1 …
Connected to localhost.
```
![alt text](https://ruslanspivak.com/lsbaws-part1/LSBAWS_socket.png "Client Server")

After we have successfully established a connection with the server using telnet, 
we can send data from the telnet client directly to the server socket.

```bash
$ telnet localhost 8888
Trying 127.0.0.1 …
Connected to localhost.
GET /hello HTTP/1.1

HTTP/1.1 200 OK
Hello, World!
```

![alt text](http://www.pybloggers.com/wp-content/uploads/2018/08/files.realpython.comsockets-tcp-flow.1da42679-50f1a1f4d71df29c3d28b246590a95fc0747f69b.jpg "Client Server Comm")


### Fitting Web Frameworks with our Web Server

![alt text](https://ruslanspivak.com/lsbaws-part2/lsbaws_part2_before_wsgi.png "Fits Well")

What if I build a framework that is not compliant with my server?

![alt text](https://ruslanspivak.com/lsbaws-part2/lsbaws_part2_after_wsgi.png "Does Not Fit")

#### Solution: Wizgy

Web server must implement the server portion of a WSGI interface and all modern Python Web Frameworks already implement the framework side of the WSGI interface, 
which allows you to use them with your Web server without ever modifying your server’s code to accommodate a particular Web framework.  

![alt text](https://ruslanspivak.com/lsbaws-part2/lsbaws_part2_wsgi_interop.png "Does Not Fit")

All we do is handover WSGI compatible app to the WSGI compatible web server
and voilla, you have your app ready to be served.  
Let us write a WSGI compatible web server first.  
`2. WSGIServerEvolution.py` has the necessary code for the WSGI compatible web server.  
The server expects a WSGI compatible app in arguments.  

Lets write a sample flask app:  
```python
from flask import Flask
from flask import Response
flask_app = Flask('flaskapp')


@flask_app.route('/hello')
def hello_world():
    return Response(
        'Hello world from Flask!\n',
        mimetype='text/plain'
    )

app = flask_app.wsgi_app
```

USAGE:  

```bash
(venv) $ python 2.\ WSGIServerEvolution.py flaskapp:app
WSGIServer: Serving HTTP on port 8888 ...
```

### How it all works?  

![alt text](https://ruslanspivak.com/lsbaws-part2/lsbaws_part2_wsgi_interface.png "WorkFlow")

1. The framework provides an ‘application’ callable (The WSGI specification doesn’t prescribe how that should be implemented)  
2. The server invokes the ‘application’ callable for each request it receives from an HTTP client. It passes a dictionary ‘environ’ containing WSGI/CGI variables and a ‘start_response’ callable as arguments to the ‘application’ callable.  
3. The framework/application generates an HTTP status and HTTP response headers and passes them to the ‘start_response’ callable for the server to store them. The framework/application also returns a response body.  
4. The server combines the status, the response headers, and the response body into an HTTP response and transmits it to the client (This step is not part of the specification but it’s the next logical step in the flow and I added it for clarity)  

### Micro WSGI Framework

```python
def app(environ, start_response):
    """A barebones WSGI application.

    This is a starting point for your own Web framework :)
    """
    status = '200 OK'
    response_headers = [('Content-Type', 'text/plain')]
    start_response(status, response_headers)
    return [b'Hello world from a simple WSGI application!\n']
```

Usage:  
```bash
(venv) $ python webserver2.py wsgiapp:app
WSGIServer: Serving HTTP on port 8888 ...
```

### From Request to Response (The Complete Story)

![alt text](https://ruslanspivak.com/lsbaws-part2/lsbaws_part2_server_summary.png "Request Response Cycle")
