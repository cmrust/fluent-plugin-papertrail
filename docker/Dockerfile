FROM fluent/fluentd:v1.1.1-debian

USER root
WORKDIR /home/fluent
ENV PATH /home/fluent/.gem/ruby/2.3.0/bin:$PATH
ENV BUILDDEPS="sudo make gcc g++ libc-dev ruby-dev libffi-dev"

RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends \
    $BUILDDEPS

RUN echo 'gem: --no-document' >> /etc/gemrc \
    && gem install fluent-plugin-systemd -v 0.3.1 \
    && gem install fluent-plugin-rewrite-tag-filter -v 1.6.0 \
    && gem install fluent-plugin-papertrail -v 0.1.3 \
    && gem install fluent-plugin-kubernetes_metadata_filter

RUN SUDO_FORCE_REMOVE=yes \
    apt-get purge -y --auto-remove \
                  -o APT::AutoRemove::RecommendsImportant=false \
                  $BUILDDEPS \
    && rm -rf /var/lib/apt/lists/* \
    && gem sources --clear-all \
    && rm -rf /tmp/* /var/tmp/* /usr/lib/ruby/gems/*/cache/*.gem

# Copy configuration files
COPY ./conf/fluent.conf /fluentd/etc/
COPY ./conf/systemd.conf /fluentd/etc/
COPY ./conf/kubernetes.conf /fluentd/etc/

# Copy plugins
COPY plugins /fluentd/plugins/

# Environment variables
ENV FLUENTD_OPT=""
ENV FLUENTD_CONF="fluent.conf"

# Overriding entrypoint, otherwise parent image causes to run as user fluent
ENTRYPOINT []

# Run Fluentd
CMD exec fluentd -c /fluentd/etc/$FLUENTD_CONF -p /fluentd/plugins $FLUENTD_OPT
