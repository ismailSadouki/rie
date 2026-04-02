### HTTP request
- GET /orders/123(the request target) HTTP/1.1(the Http version) that's the http command that been send 
- Host: 127.0.0.1:8080
- User-Agent: Manual-Http-Request (the header that tell the web server what type of browser we are using)
- Content-Length: 12 (the size of the body)
- Content-Type: application/x-www...(how the data in our body is encoded)
- {
	Body
 }
 
### HTTP response
- HTTP/1.1 200 OK (the status line)
- Content-Length: 88
- Content-Type: text/html
- Connection: Closed
- {
  <!doctype html>
  <html>
		  ....
  </html>
 }