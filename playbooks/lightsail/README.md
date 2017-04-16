### Disclaimer
This was a bit rough to cobble together and I found/worked through a handful of bugs. I still hit snags now and then I haven't fully sorted out, so don't expect perfection.

### Known issues
1. When the LetsEncrypt container sees an application deployment via the Docker daemon, it is issueing an exit on the application container:
```
2017-04-16 01:15:26,604:INFO:simp_le:1383: Certificates already exist and renewal is not necessary, exiting with status code 1.
```

### Use case
Hosting a Ghost blog on the official Ghost platform is pricy, as is a t2-micro AWS instance. Lightsail offers a cheap alternative to this, with a few constraints. I wanted to test this out and see if I could make it work for a couple hobbyist blogs.

### Requirements
This list is raw for now, more details to come.

* Ansible 2.2
* Domain name set up with all appropriate records
* Mailgun account setup properly (MX records, etc)
* CentOS based Amazon Linux instance (Ubuntu support coming in the future)
  * While there, open port 443

### Usage
1. Create your inventory by renaming us_east_1.example to us_east_1 in the same directory, and populating with your blog information

2. Run playbook
```
ansible-playbook playbooks/lightsail/ghost.yml -vvvv
```

### End result
Running containers:
```
[ec2-user@host]$ docker ps
CONTAINER ID        IMAGE                                    COMMAND                  CREATED             STATUS              PORTS                                      NAMES
ea29d6030f8e        ghost                                    "/entrypoint.sh npm s"   About an hour ago   Up About an hour    0.0.0.0:2368->2368/tcp                     ghost
8c11dd818276        bennetimo/ghost-backup                   "/entrypoint.sh sh -c"   About an hour ago   Up About an hour                                               ghost-backup
dc7ef43201b9        jrcs/letsencrypt-nginx-proxy-companion   "/bin/bash /app/entry"   About an hour ago   Up About an hour                                               letsencrypt
4d8e72398e45        jwilder/nginx-proxy                      "/app/docker-entrypoi"   About an hour ago   Up About an hour    0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   nginx-proxy
```

Hitting HTTPS with no certificate errors (replace with your domain name):
```
curl -I https://$GHOST_DOMAIN
HTTP/1.1 200 OK
Server: nginx/1.11.8
Date: Sun, 16 Apr 2017 00:47:38 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 4588
Connection: keep-alive
X-Powered-By: Express
Cache-Control: public, max-age=0
ETag: W/"11ec-pKxrBH0gc3bU2Lwqq0xa6g"
Vary: Accept-Encoding
Strict-Transport-Security: max-age=31536000
```
### References
https://github.com/jwilder/nginx-proxy
https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion
https://www.mailgun.com
https://amazonlightsail.com

### Feedback
Notice anything shitty? Notice that everything's shitty? Let me know.
