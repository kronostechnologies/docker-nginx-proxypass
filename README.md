# docker-nginx-proxypass

What it does:

  - listen any vhost on port 80
  - proxy requests to any upstream http/https backend
  - sets the host header to upstream url
  - add basic_auth if required

Example:
  - forward any request to localhost port 80 to http://perdu.com/
  - forward any request to localhost port 80 to https://ifconfig.co/
  - add basic auth to upstream servers

FEAT:
  - Uses environment variables only to reduce out-of-compose dependencies
  - Expects https to be offloaded to a LB.

Environment variables:

UPSTREAM: Comma separated list of nginx upstreams (Always use host:port)
```
upstream upstream {
  server UPSTREAM[0]
  server UPSTREAM[1]
  etc.
}
```

UPSTEAM_PROTO: Protocol of the upstream servers (default: https)
If your upstreams are http, then this should be http.
```
proxy_pass UPSTREAM_PROTO://upsteam/
```

UPSTREAM_DYNAMIC: Resolve upstream DNS on every hits (only if there is a single source)
Default: true
```
set UPSTREAM_PROTO://$server UPSTREAM
proxy_pass $server
```


HOST_HEADER: The host header passed to upstream servers.
This should be the target domain to reach.
```
proxy_set_header Host HOST_HEADER;
```

HTPASSWD: A base64 encoded htpasswd entry
```
# encode
htpasswd=$(docker run --rm -ti xmartlabs/htpasswd <username> <password> | base64 -w 0)

# decode
echo $htpasswd | base64 -d -w 0
```

ALLOWED: A comma separated list of subnets allowed as source
DENIED:  A comma separated list of subnets denied as source
```
location / {
  allow ALLOWED[0]
  allow ALLOWED[1]
  deny DENIED[0]
  deny DENIED[1]
}
```

XFORWARDED_SUBNET: A comma separated list of subnets to trust X-Forwarded-For header from
```
  real_ip_header X-Forwarded-For;
  set_real_ip_from XFORWARDED_SUBNET;
```
