FROM debian:unstable

MAINTAINER Sysdig <support@sysdig.com>

ENV SYSDIG_VERSION 0.1.1dev

LABEL RUN="docker run -i -t -v /var/run/docker.sock:/host/var/run/docker.sock -v /dev:/host/dev -v /proc:/host/proc:ro -v /boot:/host/boot:ro -v /lib/modules:/host/lib/modules:ro -v /usr:/host/usr:ro --name NAME IMAGE"

ENV SYSDIG_HOST_ROOT /host

ENV HOME /root

RUN cp /etc/skel/.bashrc /root && cp /etc/skel/.profile /root

ADD http://download.draios.com/apt-draios-priority /etc/apt/preferences.d/

RUN echo "deb http://httpredir.debian.org/debian jessie main" > /etc/apt/sources.list.d/jessie.list \
 && apt-get update \
 && apt-get install -y --no-install-recommends \
	bash-completion \
	curl \
	dkms \
	gnupg2 \
	ca-certificates \
	gcc \
	gcc-5 \
	gcc-4.9 \
	libc6-dev \
	libelf-dev \
	libelf1 \
	less \
	procps \
 && rm -rf /var/lib/apt/lists/*

# Since our base Debian image ships with GCC 5.0 which breaks older kernels, revert the
# default to gcc-4.9. Also, since some customers use some very old distributions whose kernel
# makefile is hardcoded for gcc-4.6 or so (e.g. Debian Wheezy), we pretend to have gcc 4.6/4.7
# by symlinking it to 4.9

RUN rm -rf /usr/bin/gcc \
 && ln -s /usr/bin/gcc-4.9 /usr/bin/gcc \
 && ln -s /usr/bin/gcc-4.9 /usr/bin/gcc-4.8 \
 && ln -s /usr/bin/gcc-4.9 /usr/bin/gcc-4.7 \
 && ln -s /usr/bin/gcc-4.9 /usr/bin/gcc-4.6

RUN ln -s $SYSDIG_HOST_ROOT/lib/modules /lib/modules

ADD sysdig-${SYSDIG_VERSION}-x86_64.deb /
RUN dpkg -i /sysdig-${SYSDIG_VERSION}-x86_64.deb

COPY ./docker-entrypoint.sh /

ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["bash"]
