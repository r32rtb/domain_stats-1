# alpine-domainstats Builder

Domain analysis service

This builder image constructs a Alpine Linux image for a minimalistic image.  The image should consist of only the needed dependancies for domain_stats.  It includes the latest version of pywhois which has support for a SOCKS proxy.  It also includes the latest version to top-1m.csv.

SOCKS Proxy support requirements:

     #Add the following to the docker run script
     #The environment variable for SOCKS if needed to utilize a SOCKS5 proxy.
     $  docker run --name=so-domainstats \
                        --detach \
                        --volume /var/log/domain_stats:/var/log/domain_stats \
                        -e SOCKS=someplace.proxy.com:1080 \
                        alpine-domainstats

     # Logstash will connect to DomainStats over $DOCKERNET
     docker network connect --alias domainstats $DOCKERNET so-domainstats
