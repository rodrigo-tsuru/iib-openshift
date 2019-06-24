# © Copyright IBM Corporation 2015.
#
# All rights reserved. This program and the accompanying materials
# are made available under the terms of the Eclipse Public License v1.0
# which accompanies this distribution, and is available at
# http://www.eclipse.org/legal/epl-v10.html

#FROM rhel:rhel7
FROM registry.access.redhat.com/rhel7/rhel

MAINTAINER Sam Rogers srogers@uk.ibm.com

LABEL "ProductID"="447aefb5fd1342d5b893f3934dfded73" \
      "ProductName"="IBM Integration Bus" \
      "ProductVersion"="10.0.0.16"
  
# Install IIB V10 Developer edition
RUN mkdir /opt/ibm && \
    curl http://public.dhe.ibm.com/ibmdl/export/pub/software/websphere/integration/10.0.0.16-IIB-LINUX64-DEVELOPER.tar.gz \
    | tar zx --exclude iib-10.0.0.16/tools --directory /opt/ibm && \
    /opt/ibm/iib-10.0.0.16/iib make registry global accept license silently

# Configure system
COPY kernel_settings.sh /tmp/
#RUN echo "IIB_10:" > /etc/debian_chroot  && \
#    touch /var/log/syslog && \
#    chown syslog:adm /var/log/syslog && \
RUN chmod +x /tmp/kernel_settings.sh;sync && \
    /tmp/kernel_settings.sh


# Create user to run as
RUN useradd --create-home --home-dir /home/iibuser -G mqbrkrs iibuser

# Copy in script files
COPY iib_manage.sh /usr/local/bin/
COPY iib-license-check.sh /usr/local/bin/
COPY iib_env.sh /usr/local/bin/
RUN chmod +rx /usr/local/bin/*.sh

# Set BASH_ENV to source mqsiprofile when using docker exec bash -c
ENV BASH_ENV=/usr/local/bin/iib_env.sh
ENV MQSI_MQTT_LOCAL_HOSTNAME=127.0.0.1

# Expose default admin port and http port
EXPOSE 4414 7800

USER iibuser

# Set entrypoint to run management script
ENTRYPOINT ["iib_manage.sh"]
