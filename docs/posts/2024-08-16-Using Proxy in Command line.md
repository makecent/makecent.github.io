---
layout: default
title: Using proxies in command line
parent: Posts
nav_order: 1
---


# Introduction
Sometimes, we found that after using VPN, the browser can access a specific website but the software run in command line may not. In this case, we may need to configure proxies for these software.

# Chec proxy
Normally, the VPN will set the environment variables about proxies for you when you open a terminal:
```terminal
echo $http_proxy
echo &https_proxy
```
Check if these two variable are empty. If yes, then set them to you `http://127.0.0.1:port-num` (replace the `port-num` with your VPN port).

# Check proxy
Check if you could fetch data from the website:
```terminal
curl -I google.com
```
**Note that you may find that `curl` works but `ping` did not. That's because your VPN prevent ICMP traffic (which is used by ping)**

# Set `sudo` proxies
While `sudo` does not use the environment variables defined above by default as they are of the user, to run a sudo command using the VPN proxies:
```terminal
sudo -E ...
```

# Set `docker` proxies
The docker proxies could be set by modifying the `/etc/docker/daemon.json` to manually set the proxies:
```yaml
{
    ...
    "proxies": {
      "http-proxy": "http:/127.0.0.1:prot-num",
      "https-proxy": "http:/127.0.0.1:prot-num",
      "no-proxy": "..."
    }
}
```
Then, restart the docker service to enable the modification in the configuration file:
```terminal
sudo systemctl restart docker
```

# Set `docker` containers proxies
The above section could only help you pull images using proxies from the dockerhub. But the container created by the docker does not use proxies. To make the container use proxies:
1. If does not have an image.
```terminal
docker build --build-arg http_proxy=$http_proxy --build-arg https_proxy=$https_proxy --build-arg all_proxy=$all_proxy -t your_image_name .
```
2. If an image was created.
```terminal
docker run -e http_proxy=$http_proxy -e https_proxy=$https_proxy -e all_proxy=$all_proxy -it your_image_name
```
**Note that according to [a disccussion](https://stackoverflow.com/a/64213074/10755569), you have to make sure that the VPN opens before the docker to make it works**
3. **BTW**
You could modify the dockerfile to change the ubuntu's apt source, e.g., using China mainland mirror source to make commands `apt update` and `apt install` run faster.
