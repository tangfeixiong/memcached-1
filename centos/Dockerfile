# See also:
# https://www.howtoforge.com/how-to-install-memcached-with-repcached-built-in-server-side-replication-on-debian-lenny
# https://github.com/mdounin/memcached/tree/repcached
# https://github.com/docker-library/memcached/blob/137391b8166fd61c12bc54098350181d7da378f0/debian/Dockerfile
FROM docker.io/centos:centos6
#FROM docker.io/centos:centos7

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r memcache && useradd -r -g memcache memcache

RUN INSTALL_PKGS="which git tar wget sysvinit-tools epel-release \
      ethtool device-mapper iptables tree findutils e2fsprogs xfsprogs lsof libevent" && \
    yum install -y $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all && \
    mkdir -p /var/lib/memcache
# RUN INSTALL_PKGS="which git tar wget hostname sysvinit-tools util-linux bsdtar epel-release \
#       socat ethtool device-mapper iptables tree findutils nmap-ncat e2fsprogs xfsprogs lsof libevent" && \
#     yum install -y $INSTALL_PKGS && \
#     rpm -V $INSTALL_PKGS && \
#     yum clean all && \
#     mkdir -p /var/lib/memcache

ENV MEMCACHED_VERSION 1.4.13
ENV MEMCACHED_SHA1 b19b41d968e064902dd585c68582b687e6ccdfc7
ENV REPCACHED_VERSION 2.3.1

RUN buildDeps=' \
		gcc \
		glibc \
		libevent-devel \
       automake \
       autoconf \
		make \
		perl \
	' \
	&& set -x \
	&& yum install -y $buildDeps \
    && rpm -V $buildDeps \
	&& yum clean all \
	# && wget -O memcached.tar.gz "http://memcached.org/files/memcached-$MEMCACHED_VERSION.tar.gz" \
	# && echo "$MEMCACHED_SHA1  memcached.tar.gz" | sha1sum -c - \
	# && mkdir -p /usr/src/memcached \
	# && tar -xzf memcached.tar.gz -C /usr/src/memcached --strip-components=1 \
	# && rm memcached.tar.gz \
    && git clone -b repcached https://github.com/mdounin/memcached /usr/src/memcached \
	&& cd /usr/src/memcached \
    && ./autogen.sh \
	&& ./configure --prefix=/ --enable-replication \
	&& make -j$(nproc) \
	&& make install \
	&& cd / && rm -rf /usr/src/memcached \
    && yum erase -y $buildDeps
	# && yum remove -y $buildDeps

# COPY docker-entrypoint.sh /usr/local/bin/
# RUN ln -s usr/local/bin/docker-entrypoint.sh /entrypoint.sh # backwards compat
# RUN echo " \n\
#   #!/bin/bash \n\ 
#   set -e \n\
#   if [ "${1#-}" != "$1" ]; then \n\ 
# 	  set -- memcached "$@" \n\
#   fi \n\
#   exec "$@" \n\ 
#   " >> /bin/docker-entrypoint.sh \
#   && chmod +x /bin/docker-entrypoint.sh

# ENTRYPOINT ["docker-entrypoint.sh"]

USER memcache
EXPOSE 11211
CMD ["memcached", "-u", "root"]
