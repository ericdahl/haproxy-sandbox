# test-haproxy-sessions

experimenting with haproxy proxy config/logging/etc


```
| %TR  | time to receive the full request from 1st byte| numeric     |
| %Tw  | total time spent in the queues waiting for a connection slot
| %Tc  | Tc total time to establish the TCP connection to the server
| %Tr  | server response time
| %Ta  | total active time for the HTTP request, between the moment the proxy
    received the first byte of the request header and the emission of the last
    byte of the response body
```