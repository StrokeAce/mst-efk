FROM fluent/fluentd:v0.14

RUN echo "http://mirrors.aliyun.com/alpine/v3.5/main/" > /etc/apk/repositories \
    && gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/

RUN apk add --update --virtual .build-deps autoconf automake libtool sudo build-base ruby-dev ca-certificates openssl \
    && update-ca-certificates \
    && wget https://github.com/kelseyhightower/confd/releases/download/v0.15.0/confd-0.15.0-linux-amd64 -O /usr/sbin/confd \
    && chmod +x /usr/sbin/confd \
    && sudo apk add geoip geoip-dev \
    && sudo gem install geoip-c\
        fluent-plugin-elasticsearch \
        fluent-plugin-record-reformer \
        fluent-plugin-concat \
        fluent-plugin-split_record \
        fluent-plugin-geoip:'< 1.0.0' \
        fluent-plugin-beats \
        fluent-plugin-rewrite-tag-filter \
    && sudo gem sources --clear-all \
    && apk del .build-deps \
    && rm -rf /var/cache/apk/* \
        /home/fluent/.gem/ruby/2.3.0/cache/*.gem

COPY config /etc/confd
COPY conf/fluent.conf /fluentd/etc/fluent.conf

RUN chown -R 1000 /etc/confd
CMD /usr/bin/fluentd -d /tmp/pid -c /fluentd/etc/fluent.conf -p /fluentd/plugins && confd
