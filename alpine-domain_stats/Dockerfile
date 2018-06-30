FROM alpine

MAINTAINER Roy Sprague roy.sprague@gmail.com

RUN apk add --update --no-cache --virtual .build-deps \
      && apk add --no-cache --virtual .build-deps \
        build-base \
        git \
        mercurial \
        py2-pip \
      && apk add --update --no-cache python2 \
      && pip install PySocks \
      && pip install six \
      && rm -rf /var/cache/apk/* \
      && hg clone https://bitbucket.org/richardpenman/pywhois \
      && chmod +x pywhois/setup.py \
      && cd pywhois && python setup.py install && cd / \
      && rm -rf /pywhois \
      && mkdir /opt && cd /opt && git clone https://github.com/MarkBaggett/domain_stats.git \
      && wget http://s3.amazonaws.com/alexa-static/top-1m.csv.zip \
      && rm -rf /opt/domain_stats/top-1m.csv \
      && unzip -o top-1m.csv.zip -d /opt/domain_stats \
      && rm top-1m.csv.zip \
      && find /usr/local \
          \( -type d -a -name test -o -name tests \) \
          -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
          -exec rm -rf '{}' + \
      && runDeps="$( \
          scanelf --needed --nobanner --recursive /usr/local \
                  | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
                  | sort -u \
                  | xargs -r apk info --installed \
                  | sort -u \
      )" \
      && apk add --virtual .rundeps $runDeps \
      && apk del .build-deps \ 
      && mkdir /var/log/domain_stats \
      && ln -sf /dev/stderr /var/log/domain_stats/domain_stats.log \
      && adduser -Ds /bin/sh domain_stats \
      && chown -R domain_stats: /opt/domain_stats

USER domain_stats

EXPOSE 20000

STOPSIGNAL SIGTERM

CMD /usr/bin/python /opt/domain_stats/domain_stats.py -ip 0.0.0.0 20000 -a /opt/domain_stats/top-1m.csv --preload 0
