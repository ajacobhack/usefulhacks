# Technologies

{% code overflow="wrap" %}
```bash
# Whatweb
whatweb http://IP.or.URL:8888
whatweb -a 1 http://IP.or.URL:8888
whatweb -a 3 http://IP.or.URL:8888

# Wappalizer: browser plugin

# SSL scan
sslscan <IP> <puerto>
sslscan 10.10.145.145       # default 443
sslscan 10.10.145.145 8888  # custom port with ssl/tls

sslscan <url>
sslscan https://domain.com

## CMS Scan
# Wordpress
wpscan --url <IP>/<blog> -e vp,u
```
{% endcode %}
