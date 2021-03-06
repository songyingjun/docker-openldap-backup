FROM osixia/light-baseimage:1.1.1
MAINTAINER Bertrand Gouny <bertrand.gouny@osixia.net>


ARG LDAP_OPENLDAP_GID
ARG LDAP_OPENLDAP_UID

# Add openldap user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
# If explicit uid or gid is given, use it.
RUN if [ -z "${LDAP_OPENLDAP_GID}" ]; then groupadd -r openldap; else groupadd -r -g ${LDAP_OPENLDAP_GID} openldap; fi \
    && if [ -z "${LDAP_OPENLDAP_UID}" ]; then useradd -r -g openldap openldap; else useradd -r -g openldap -u ${LDAP_OPENLDAP_UID} openldap; fi

# Install OpenLDAP, ldap-utils and ssl-tools from baseimage and clean apt-get files
# sources: https://github.com/osixia/docker-light-baseimage/blob/stable/image/tool/add-service-available
#          https://github.com/osixia/docker-light-baseimage/blob/stable/image/service-available/:ssl-tools/download.sh
RUN apt-get -y update \
    && /container/tool/add-service-available :ssl-tools \
	  && LC_ALL=C DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
       ldap-utils \
       libsasl2-modules \
       libsasl2-modules-db \
       libsasl2-modules-gssapi-mit \
       libsasl2-modules-ldap \
       libsasl2-modules-otp \
       libsasl2-modules-sql \
       openssl \
       slapd \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install cron from baseimage and remove .cfss and slapd services inherited from openldap image
# remove also previous default environment files, they are not needed.
# sources: https://github.com/osixia/docker-light-baseimage/blob/stable/image/tool/add-service-available
#          https://github.com/osixia/docker-light-baseimage/blob/stable/image/service-available/:cron/download.sh
RUN apt-get -y update \
		&& /container/tool/add-multiple-process-stack \
		&& apt-get clean \
		&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Add service directory to /container/service
ADD service /container/service

# Use baseimage install-service script
# https://github.com/osixia/docker-light-baseimage/blob/stable/image/tool/install-service
RUN /container/tool/install-service

# Add default env variables
ADD environment /container/environment/98-default

# Set backup data in a data volume
VOLUME ["/data/backup"]
