# Nginx based proxy for S3 using Docker
## Overview
A simple service to allow you to access your S3 buckets without needing to provide 
your clients with your secret keys. Hopefully this should go without saying that 
this should be in a secured environment, do not allow global access, as all 
GET/PUT/DELETE commands will be executed against your private buckets.

Based upon the osterzel build, with additional features such as setting cache sizes 
and expiration times.
Serve your static homepage from S3 while keeping the bucket private by proxying
it through nginx - running in docker. This could also allow you to store larger files 
for processing in S3, without the overhead of sharing secured keys with all microservices. 

## Usage

Clone this repo:

    git clone --recursive https://github.com/irate-badger/docker-nginx-s3proxy

Build image:

    cd docker-nginx-s3proxy
    docker build -t docker-nginx-s3proxy .


Run a container from that image:

    docker run \
    -e S3PROXY_BUCKET_NAME="<S3_BUCKET_NAME>" \
    -e S3PROXY_AWS_ACCESS_KEY="<AWS_ACCESS_KEY>" \
    -e S3PROXY_AWS_SECRET_KEY="<AWS_SECRET_KEY>" \
    -p 10080:80 \
    -d docker-nginx-s3proxy

Or cloning from Docker Hub:

    docker run \
    -e S3PROXY_BUCKET_NAME="<S3_BUCKET_NAME>" \
    -e S3PROXY_AWS_ACCESS_KEY="<AWS_ACCESS_KEY>" \
    -e S3PROXY_AWS_SECRET_KEY="<AWS_SECRET_KEY>" \
    -p 10080:80 \
    -d iratebadger/docker-nginx-s3proxy
    
Combine with an [nginx front](https://hub.docker.com/r/iratebadger/nginx/) so that you can
have multiple buckets fronted by the same proxy.

## Optional extras
### AWS Region
Amazon appear to enforce setting your region in the URL, but not always. If you are getting 307 responses \
then setting your region with the following might help

    S3PROXY_AWS_REGION="<AWS_REGION>"

### Max Body Size
If you are also using this to PUT to S3, then you can set the additional environment variable:
    
    MAX_BODY_SIZE="<MAX_BODY_SIZE>" 

See [Nginx documentation](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_max_body_size) for correct settings

### Proxy Cache
The proxy cache has some settings which can be configured:

    PROXY_BUFFERING Default:on The switch to enable or disable the proxy, set to off if you don't want it
    PROXY_CACHE_SIZE Default:500m The amount of disk space the proxy will use
    PROXY_KEY_SIZE Default:10m The size of the key store, 10m gives about 80000 keys
    PROXY_INACTIVE_AGE Default:12h The maxiumum age of a file in the cache that is inactive before it is purged.

## Known issues

* OSX users: watch out for boot2docker's clock going bad, run this to fix:

      boot2docker ssh sudo ntpclient -s -h pool.ntp.org

## Credit where credit is due

Based on:
* [https://registry.hub.docker.com/_/debian/](https://registry.hub.docker.com/_/debian/)
* [http://nginx.org/](http://nginx.org/)
* [https://github.com/anomalizer/ngx_aws_auth](https://github.com/anomalizer/ngx_aws_auth)
* [https://github.com/kreuzwerker/envplate](https://github.com/kreuzwerker/envplate)
