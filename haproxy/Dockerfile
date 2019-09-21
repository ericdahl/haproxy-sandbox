FROM haproxy:1.9

RUN apt-get update -y && apt-get install -y curl host procps man

COPY ./usr/local/etc/haproxy/haproxy.cfg /usr/local/etc/haproxy/haproxy.cfg