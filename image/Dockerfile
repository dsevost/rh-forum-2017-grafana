FROM rhel7

MAINTAINER dmitry sevostyanov

COPY grafana.repo /etc/yum.repos.d 

RUN \
    yum \
        --disablerepo=\* --enablerepo=rhel-7-server-rpms --enablerepo=grafana \
        install -y --setopt='tsflags=nodocs' \
            grafana \
    && \
    rm -rf /var/cache/yum/*

ARG DATA_DIR
ARG GF_INSTALL_PLUGINS
ARG LOG_DIR
ARG PLUGINS_DIR

ENV \
    DATA_DIR=${DATA_DIR:-/usr/share/grafana/data} \
    GF_INSTALL_PLUGINS=${GF_INSTALL_PLUGINS:-hawkular-datasource} \
    LOG_DIR=${LOG_DIR:-/var/log/grafana} \
    PLUGINS_DIR=${PLUGIN_DIR:-/usr/share/grafana/plugins}

RUN \
    IFS=',' ; \
    for p in ${GF_INSTALL_PLUGINS}; do \
        echo "Installing plugin: $p" ; \
        grafana-cli --pluginsDir ${PLUGINS_DIR} plugins install ${p} || true ; \
    done

RUN \
    chmod 664 -R /etc/grafana /usr/share/grafana /var/lib/grafana /var/log/grafana ; \
    find /etc/grafana /usr/share/grafana /var/lib/grafana /var/log/grafana -type d -exec chmod 775 {} \;
    
CMD \
    /usr/sbin/grafana-server \
        --config=${CONF_FILE} \
        --pidfile=${PID_FILE} \
        cfg:default.paths.logs=${LOG_DIR} \
        cfg:default.paths.data=${DATA_DIR} \
        cfg:default.paths.plugins=${PLUGINS_DIR}

