# PCB Docker Registry
A docker-compose service which will boot up PCB's private docker registry and an nginx container to proxy requests to it. Provided that you have an SSL certificate (or a self-signed certificate), this service will also work locally.

## Set up
1. Create a [htpasswd](https://httpd.apache.org/docs/2.4/programs/htpasswd.html) file named `registry.password` and place it in the `keys/authentication` directory. You'll also need a username and password inside it.
2. Place SSL certificates inside `keys/certs`. The nginx container expects a file called `domain.crt` and `domain.key`. These can be generated using [Let's Encrypt](https://letsencrypt.org/).
3. Run `docker-compose up -d` to boot up the service.

## Testing
* CLI: `curl https://USERNAME:PASSWORD@192.168.99.100:5043/v2/`  
  If you can see `{}`, it's working.

* Browser: `https//192.168.99.100:5043/v2/`  
  You should be greeted with an authentication prompt for a username and password. If you can see `{}`, it's working.

## Using the registry
When pushing a docker image, rather than logging into Docker Hub, use the registry's address: `docker login https://192.168.99.100:5043`

# Notes
### Why isn't this running on the host's 443 port?
It can and it's probably preferable to. However, on PCB we have multiple services running - some of which have their own nginx container. Only one nginx container can listen to port 443 so instead we send requests here via an nginx reverse proxy.

### Why aren't you just using Docker Hub?
We have sensitive data baked into our images, so naturally we need a private location to store them.

### My SSL certificate only has .pem files
Assuming you used Let's Encrypt...
* If you have a file called **privkey.pem** rename that to **domain.key**.
* If you have a file called **cert.pem** rename that to **domain.crt**