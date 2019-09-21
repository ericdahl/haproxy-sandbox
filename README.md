# haproxy-sandbox

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

## master-worker reload observations

### Test - haproxy 1.9.10

local -> haproxy -> nginx
netcat to emulate persistent HTTP

#### Test 1 - no reload
1. GET - success
2. repeat 1 forever - success

#### Test 2 - reload slow
1. GET - success
2. GET - success
3. `kill -SIGUSR2 1`
4. GET - success
5. GET - fail - TCP RST - while sending headers

#### Test 3 - reload fast

1. `curl --limit-rate 1 -v http://localhost:1111/?[1-100000]`
2. `kill -SIGUSR2 1` many times
3. observe `Connection: close` and clean re-use 


```
seq 100 | xargs -P 0 -n 1 curl 'http://192.168.99.100:8080/drip?numbytes=100&duration=10&delay=0'
```

```
 for i in $(seq 100) ; do curl 'http://192.168.99.100:8080/drip?numbytes=100&duration=10&delay=0' & sleep 3 ; done
``` 
 
``` 
root         1     0  0 21:24 ?        00:00:01 haproxy -W -db -f /usr/local/etc/haproxy/haproxy.cfg -sf 9001 8996 8971 8986 8956
root      8956     1  0 22:01 ?        00:00:00 haproxy -W -db -f /usr/local/etc/haproxy/haproxy.cfg -sf 8951 8946 8941 8926 8911
root      8971     1  0 22:01 ?        00:00:00 haproxy -W -db -f /usr/local/etc/haproxy/haproxy.cfg -sf 8966 8926 8956 8951 8946 8941
root      8986     1  0 22:02 ?        00:00:00 haproxy -W -db -f /usr/local/etc/haproxy/haproxy.cfg -sf 8981 8946 8971 8941 8951 8956
root      8996     1  0 22:02 ?        00:00:00 haproxy -W -db -f /usr/local/etc/haproxy/haproxy.cfg -sf 8991 8986 8956 8946 8971 8941 8951
root      9001     1  0 22:02 ?        00:00:00 haproxy -W -db -f /usr/local/etc/haproxy/haproxy.cfg -sf 8996 8971 8986 8956
root      9006     1  0 22:02 ?        00:00:00 haproxy -W -db -f /usr/local/etc/haproxy/haproxy.cfg -sf 9001 8996 8971 8986 8956
```