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