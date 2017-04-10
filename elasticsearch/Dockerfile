FROM fluent/fluentd:stable-debian-onbuild
MAINTAINER zhibo@rancher.com

USER root
COPY .apt.sources.list /etc/apt/sources.list

COPY entry.sh /usr/bin/
COPY fluent-journald.conf /fluentd/etc/fluent-journald.conf

RUN apt-get update \
 && apt-get upgrade -y \
 && apt-get install -y --no-install-recommends \
            curl \
            jq \
 && rm -rf /var/lib/apt/lists/* \
 && rm -rf /tmp/* /var/tmp/*

RUN buildDeps="sudo make gcc g++ libc-dev ruby-dev" \
 && apt-get update \
 && apt-get install -y --no-install-recommends $buildDeps \

 # cutomize following instruction as you wish
 && sudo -u fluent gem install \
        fluent-plugin-elasticsearch \
        fluent-plugin-systemd \
        systemd-journal \

 && sudo -u fluent gem sources --clear-all \
 && SUDO_FORCE_REMOVE=yes \
    apt-get purge -y --auto-remove \
                  -o APT::AutoRemove::RecommendsImportant=false \
                  $buildDeps \
 && rm -rf /var/lib/apt/lists/* \
           /home/fluent/.gem/ruby/2.3.0/cache/*.gem

#USER fluent

ENTRYPOINT ["/usr/bin/entry.sh"]
CMD ["fluentd", "-c", "/fluentd/etc/fluent.conf"]
