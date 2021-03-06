FROM ubuntu:latest

ENV DEBIAN_FRONTEND noninteractive

ADD https://raw.githubusercontent.com/dceoy/print-github-tags/master/print-github-tags /usr/local/bin/print-github-tags

RUN set -e \
      && ln -sf bash /bin/sh

RUN set -e \
      && apt-get -y update \
      && apt-get -y dist-upgrade \
      && apt-get -y install --no-install-recommends --no-install-suggests \
        autoconf ca-certificates curl gcc libbz2-dev libcurl4-gnutls-dev \
        libgsl-dev libperl-dev liblzma-dev libssl-dev libz-dev make \
      && apt-get -y autoremove \
      && apt-get clean \
      && rm -rf /var/lib/apt/lists/*

RUN set -eo pipefail \
      && chmod +x /usr/local/bin/print-github-tags \
      && print-github-tags --release --latest samtools/bcftools \
        | xargs -i curl -SL \
          https://github.com/samtools/bcftools/releases/download/{}/bcftools-{}.tar.bz2 \
          -o /tmp/bcftools.tar.bz2 \
      && tar xvf /tmp/bcftools.tar.bz2 -C /usr/local/src --remove-files \
      && mv /usr/local/src/bcftools-* /usr/local/src/bcftools \
      && cd /usr/local/src/bcftools \
      && autoheader \
      && autoconf \
      && ./configure --enable-libgsl --enable-perl-filters \
      && make \
      && make install

RUN set -eo pipefail \
      && print-github-tags --release --latest dellytools/delly \
        | xargs -i curl -SL \
          https://github.com/dellytools/delly/releases/download/{}/delly_{}_linux_x86_64bit \
          -o /usr/local/bin/delly \
      && chmod +x /usr/local/bin/delly

ENV BCFTOOLS_PLUGINS /usr/local/src/bcftools/plugins

ENTRYPOINT ["/usr/local/bin/delly"]
