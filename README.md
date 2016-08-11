# nginx based proxy for s3 - docker

Serve your static homepage from S3 while keeping the bucket private by proxying
it through nginx - running in docker.

# Usage

Clone this repo:

    git clone --recursive https://github.com/osterzel/docker-nginx-s3proxy.git

Build image:

    cd docker-nginx-s3proxy
    docker build -t nginx-s3proxy .


Run a container from that image:

    docker run \
    -e S3PROXY_BUCKET_NAME="<S3_BUCKET_NAME>" \
    -e S3PROXY_AWS_ACCESS_KEY="<AWS_ACCESS_KEY>" \
    -e S3PROXY_AWS_SECRET_KEY="<AWS_SECRET_KEY>" \
    -p 10080:80 \
    -d nginx-s3proxy

# Optional extras

## Max Body Size
If you are also using this to PUT to S3, then you can set the additional environment variable:
    
    MAX_BODY_SIZE="<MAX_BODY_SIZE>" 

See [Nginx documentation](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_max_body_size) for correct settings

## Proxy Caching
For proxy caching there are the additional arguments avaialble:  
    PROXY_CACHE_VALID="<TIME>"  
    PROXY_CACHE_VALID_200="<TIME>"  
    PROXY_CACHE_VALID_301="<TIME>"  
    PROXY_CACHE_VALID_302="<TIME>"  
    PROXY_CACHE_VALID_404="<TIME>"  
    PROXY_CACHE_VALID_ANY="<TIME>"  

If the proxy cache without a response code is used, then it will cache 200, 301, and 302 responses with the stated duration. See [#proxy_cache_valid]{http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_valid} for more details on the options for TIME.


# Known issues

* OSX users: watch out for boot2docker's clock going bad, run this to fix:

      boot2docker ssh sudo ntpclient -s -h pool.ntp.org

# Credit where credit is due

Based on:
* [https://registry.hub.docker.com/_/debian/](https://registry.hub.docker.com/_/debian/)
* [http://nginx.org/](http://nginx.org/)
* [https://github.com/anomalizer/ngx_aws_auth](https://github.com/anomalizer/ngx_aws_auth)
* [https://github.com/kreuzwerker/envplate](https://github.com/kreuzwerker/envplate)
