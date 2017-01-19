# nginx based proxy for s3 - docker

Based upon the osterzel build, with additional features such as setting cache sizes and expiration times.
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

## Optional extras
# AWS Region
Amazon appear to enforce setting your region in the URL, but not always. If you are getting 307 responses \
then setting your region with the following might help

    S3PROXY_AWS_REGION="<AWS_REGION>"

# Max Body Size
If you are also using this to PUT to S3, then you can set the additional environment variable:
    
    MAX_BODY_SIZE="<MAX_BODY_SIZE>" 

See [Nginx documentation](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_max_body_size) for correct settings

# Proxy Cache
The proxy cache has some settings which can be configured:

    PROXY_CACHE_SIZE Default:500m The amount of disk space the proxy will use
    PROXY_KEY_SIZE Default:10m The size of the key store, 10m gives about 80000 keys
    PROXY_INACTIVE_AGE Default:12h The maxiumum age of a file in the cache that is inactive before it is purged.

# Known issues

* OSX users: watch out for boot2docker's clock going bad, run this to fix:

      boot2docker ssh sudo ntpclient -s -h pool.ntp.org

# Credit where credit is due

Based on:
* [https://registry.hub.docker.com/_/debian/](https://registry.hub.docker.com/_/debian/)
* [http://nginx.org/](http://nginx.org/)
* [https://github.com/anomalizer/ngx_aws_auth](https://github.com/anomalizer/ngx_aws_auth)
* [https://github.com/kreuzwerker/envplate](https://github.com/kreuzwerker/envplate)
