---
date: '2026-07-16T21:02:05+02:00'
draft: true
title: 'Putting the Snap Behind a Reverse Proxy'
---
<h1 align="center">Reverse proxy configuration</h1>

## Multiple domain names, sites, ports and services

TCP ports 80 and 443 are quite the commodity. You only have one of each for your public IPv4 address. 

The only way to support multiple sites and services on those ports, without changing the default ports, is to have a local  web server or a [reverse proxy](https://en.wikipedia.org/wiki/Reverse_Proxy) server that's responsible for listening on those ports, taking a look at the domain names being visited and forwarding the traffic accordingly. The plus side being, that a reverse proxy server could handle SSL certificates for HTTPS encryption if required.

See [reverse proxy documentation](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/reverse_proxy_configuration.html)

The snap defaults to using ports 80 and 443, but if you're in the above situation, fear not: you can forward traffic to Nextcloud just like you do for your other sites. The first thing to do is get your snap listening somewhere _other_ than those two ports. There are two ways to do this:

 1. Simply request that the snap listen on other ports
 2. Isolate the snap in another network (another machine, perhaps a VM or container)

We'll use (1) here since it's easy, and it's easy to apply the lessons learned to (2) anyway.

So let's change the snap ports away from 80 for HTTP and 443 for HTTP, to 81 and 444, respectively:

    sudo snap set nextcloud ports.http=81 ports.https=444

See [port configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Port-configuration) for more details.

The next step depends on the proxy software you're using.

# HAProxy

HAProxy is the author's proxy of choice. It couldn't be simpler to setup. First, define a backend for both HTTP and HTTPS connections to the snap:

```
backend nextcloud-http                                                                                                                                                                                                                        
        mode http                                                                                                      
        balance roundrobin                                                                                                                                                                                                                    
        option forwardfor                                  
        option httpchk HEAD /
        http-check send ver HTTP/1.1 hdr Host localhost
        server nextcloud 127.0.0.1:81 check                                                                            
        timeout connect 4s                                 
        timeout server 4s                 
                                                           
backend nextcloud-https                                    
        mode tcp                                                                                                       
        balance roundrobin
        option httpchk HEAD /                                                                                                                                                                                                                 
        http-check send ver HTTP/1.1 hdr Host localhost                                                                                                                                                                                       
        option ssl-hello-chk                   
        server nextcloud 127.0.0.1:444 check                                                                                                                                                                                                  
        timeout connect 4s                                                                                             
        timeout server 4s      
```

Now make sure the HTTP and HTTPS frontends forward traffic to the backends accordingly:

```
frontend http                                                                                                          
        bind *:80                                                                                                      
        mode http                                                                                                                                                                                                                             
                                                                                                                                                                                                                                              
        acl host_nextcloud hdr(host) -i <domain name>                                                                  
        use_backend nextcloud-http if host_nextcloud       
        timeout client 4s                                                                                              
                                                                                                                       
frontend https                                             
        bind *:443                                         
        mode tcp                                                                                                       
                                                                                                                       
        tcp-request inspect-delay 5s                       
        tcp-request content accept if { req_ssl_hello_type 1 }
                                                                                                                       
        acl host_nextcloud req_ssl_sni -i <domain name>                                                                
        use_backend nextcloud-https if host_nextcloud                                                                  
        timeout client 4s    
```

That's it. You can continue to use `nextcloud.enable-https` as normal.

# NGINX

NGINX is a little more difficult to setup as it wants badly to terminate SSL connections for you. If that works for you, great-- have the [proxy machine handle the SSL stuff](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager), and just leave the snap with HTTPS disabled. There are all sorts of docs out there for that, so I won't document it here.

However, if you want the snap to continue managing Let's Encrypt certificates for you, that won't work. There _is_ a way to get NGINX to pass along SSL-encrypted traffic without touching it, but it sort of breaks anything else you have on SSL. We'll cover it anyway. The author is using Ubuntu 16.04 here; keep that in mind if you're using something else, your config may differ from the steps given here.

First, let's create a configuration for HTTP. Create a new site in `/etc/nginx/sites-available/`, perhaps `/etc/nginx/sites-available/nextcloud`. Make it look like this:

```
server {
        listen 80;

        # If you want to also support ipv6
        listen [::]:80;

        server_name <domain name>;

        location / {
                proxy_pass_header   Server;
                proxy_set_header    Host $host;
                proxy_set_header    X-Real-IP $remote_addr;
                proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header    X-Forwarded-Proto $scheme;
                proxy_pass          http://127.0.0.1:81;
        }
}
```

You can symlink that into `/etc/nginx/sites-enabled/`, restart nginx, and HTTP would start proxying to the snap.

Unfortunately, HTTPS isn't quite that easy, as the only way NGINX supports being an SSL passthrough is if we setup a stream server instead of an HTTP one. We can't do this by putting a new site in `/etc/nginx/sites-available/`, since those are loaded by `/etc/nginx/nginx.conf` into the `http` block. We need to add a `stream` block here. Let's do that by editing `/etc/nginx/nginx.conf` to include another set of files into the root config (rather than into the `http` block), something like this:

```
<snip>
pid /run/nginx.pid;

##
# This is the only line you need to add. The rest is just context
# and should already be there.
##
include /etc/nginx/tcpconf.d/*;

events {
        worker_connections 768;
        # multi_accept on;
}

http {
   # <snip>
```

Now create that directory:

    sudo mkdir /etc/nginx/tcpconf.d

And add a new configuration file called `/etc/nginx/tcpconf.d/nextcloud` and make it look something like this:

```
stream {
        server {
                listen 443;
                listen [::]:443;
                proxy_pass 127.0.0.1:444;
        }
}
```

Now restart NGINX, and you should be up and running. You can continue to use `nextcloud.enable-https` as normal. The big problem with this config is that it doesn't support SNI. It looks like you can make it work with the [ngx_stream_ssl_preread](https://nginx.org/en/docs/stream/ngx_stream_ssl_preread_module.html) module, but those docs say the module isn't built by default and now the author is tired. If you figure it out, feel free to update this post.

**Note:** Nextcloud's automatic hostname detection can fail when behind a proxy; you might notice it redirecting incorrectly. If this happens, override the automatic detection (including the port if necessary), e.g.:

    sudo nextcloud.occ config:system:set overwritehost --value="example.com:81"


## NGINX Optional Custom Path Location For Reverse Proxy

**Why?**
* One domain, server or IP address for one instance maybe wasteful unless you need the dedicated resources
* Maybe you like https://mydomain.com/mycustompath/ for accessing your Nextcloud instance(s)
* Maybe you are a developer

**Here we have made the assumptions that:**
1. You have already installed Nextcloud using Snap
2. You have already setup and enabled secure http for Nginx
3. Now you want to host your Nextcloud instance using a simple path addition to your domain

**Variables**
* "mydomain.com" gets replaced with your actual domain name (i.e. "nextcloud.com" for example)
* "/mycustompath" gets replaced with your actual custom path name (i.e. "/nextcloud" for example)

```
$ nextcloud.occ config:system:set overwritehost --value="mydomain.com"
$ nextcloud.occ config:system:set overwriteprotocol --value="https"
$ nextcloud.occ config:system:set overwritewebroot --value="/mycustompath"
$ nextcloud.occ config:system:set overwrite.cli.url --value="https://mydomain.com/mycustompath"
```

Now we must modify our Nginx config to recognize the changes.
You only need to change the "location" to our "/mycustompath" value and add your localhost ip address.

```
location /mycustompath {
   rewrite ^/mycustompath(.*) $1 break;
   proxy_pass http://localhost:81;
}
```
## Setting overwrite parameters

[Nextcloud overwrite parameters](https://docs.nextcloud.com/server/27/admin_manual/configuration_server/reverse_proxy_configuration.html#overwrite-parameters) such as `overwriteprotocol` and `overwritehost` are two configs that absolutely depend on your reverse proxy setup.
 
Be aware that some devices require an additional setting in `config.php` if your Nextcloud snap instance is behind a [reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy#nginx-optional-custom-path-location-for-reverse-proxy).

For internet facing Nextcloud snap that handles encryption itself or a pass-through reverse proxy is present it shouldn't be necessary to set `overwriteprotocol` and `overwritehost`. 

edit `config.php` and add overwrite parameters
```
    'overwriteprotocol' => 'https',
```
```
    'overwritehost' => 'mydomain.com',
```

or issue command:

```
sudo nextcloud.occ config:system:set overwriteprotocol --value="https"

```

```
sudo nextcloud.occ config:system:set overwritehost --value="my.domain.com"

```


----
## Proxy options

* [NGINX proxy manager reverse proxy with termination](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager)
* [Hosts & FQDN configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap)
* [Nextcloud reverse proxy documentation](https://docs.nextcloud.com/server/27/go.php?to=admin-reverse-proxy)
