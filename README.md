# Install Guide

Just wallet is a single page web application. It should be deployed in a web server (nginx, apache...) and end user can access it by any browser (chrome, firefox). It can be deployed directly or by docker.



## Deploy Directly

### Install Just node and configure

please go to the [Just Docs](https://github.com/Elysium-Wonderland/Justice-Universe/blob/master/docs/supernode.md)

### Install Nginx

please go to the [Nginx Docs](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)

### Configure Nginx as proxy of Just Node

For security, Just rpc doesn't support Cross-Origin Resource Sharing (CORS). So a proxy is required if wallet running a browser wants to call RPC from Justd. We suggest using Nginx to act the proxy. CORS should be enabled in the Nginx configuration as well.

Below is an sample to config the proxy in Nginx:

``` nginx
location /api/ {
if ($request_method = 'OPTIONS') {
        return 204;
    }
    add_header Access-Control-Allow-Origin $http_origin always;
    add_header Access-Control-Allow-Credentials 'true' always;
    add_header Access-Control-Allow-Methods 'POST,GET,OPTIONS' always;
    add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type' always;
    add_header Access-Control-Expose-Headers 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Content-Range,Range';
    proxy_pass   JUSTD_RPC_URL;
    proxy_set_header Host $host;
    proxy_set_header X-Real-Ip $remote_addr;
    proxy_set_header X-Forwarded-For $http_x_forwarded_for;
}
```

> JUSTD_RPC_URL should be defined as the rpc endpoint of justd node. Default port is 1317. If justd and nginx are in the same host, it would be http://127.0.0.1:1317/.

### Download Just Wallet

Download the Just Wallet, extract it and copy it to a folder.

### Configure Nginx as web server of Just Wallet

Just Wallet is a web application. You must deploy in a web server. We take Nginx as a sample.

> Just Wallet is a VUE based Simple Page Application and router mode is history. So all sub link should be rewritten in Nginx.

> application.json is the configuration file. It must be configured in addition.

``` Nginx
location /application.json {
    root [just-wallet folder];
}
location / {
    root  [just-wallet folder];
    try_files $uri $uri/ @router;
    index  index.html;
}
location @router {
    rewrite ^.*$ /index.html last;
}
```

### Configure Just Wallet

Configure the application in just wallet folder. Change the rpcUrl to the Nginx web applcation url.

### Start Nginx



## Deploy by Docker

### Install Just node and configure

please go to the [Just Docs](https://github.com/Elysium-Wonderland/Justice-Universe/blob/master/docs/supernode.md)

### Docker pull

docker pull elysiumwonder/just-wallet:latest

### Run Docker

docker run -p 8080:8080 --name just-wallet --env WEB_SERVER_URL='[web_server_url]' --env JUST_NODE_URL='[just_node_url]' elysiumwonder/just-wallet:latest
> web_server_url is the real endpoint of the wallet application. User can input the url in browser (chrome) to access the wallet.
> just_node_url is the rpc endpoint of just node installed above.