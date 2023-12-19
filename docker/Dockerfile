FROM debian:bookworm AS samtools_build

ARG HTSLIB_VERSION=1.19
ARG SAMTOOLS_VERSION=1.19
ARG BCFTOOLS_VERSION=1.19

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get upgrade -y && \
    apt-get install -qq -y --no-install-recommends \
      git \
      ca-certificates \
      autotools-dev \
      build-essential \
      autoconf \
      automake \
      jq \
      libbz2-dev \
      libcurl4-openssl-dev \
      libgd-dev \
      liblzma-dev \
      libssl-dev \
      libncurses5-dev \
      curl

RUN cd /tmp && \
    curl -sSL -O "https://github.com/samtools/htslib/releases/download/${HTSLIB_VERSION}/htslib-${HTSLIB_VERSION}.tar.bz2" && \
    tar xfj htslib-${HTSLIB_VERSION}.tar.bz2 && \
    cd htslib-${HTSLIB_VERSION} && \
    ./configure --enable-plugins && \
    make && \
    make install && \
    ldconfig

RUN cd /tmp && \
    curl -sSL -O "https://github.com/samtools/samtools/releases/download/${SAMTOOLS_VERSION}/samtools-${SAMTOOLS_VERSION}.tar.bz2" && \
    tar xfj samtools-${SAMTOOLS_VERSION}.tar.bz2 && \
    cd samtools-${SAMTOOLS_VERSION} && \
    ./configure --with-htslib=system && \
    make install

RUN cd /tmp && \
    curl -sSL -O "https://github.com/samtools/bcftools/releases/download/${BCFTOOLS_VERSION}/bcftools-${BCFTOOLS_VERSION}.tar.bz2" && \
    tar xfj bcftools-${BCFTOOLS_VERSION}.tar.bz2 && \
    cd bcftools-${BCFTOOLS_VERSION} && \
    ./configure --with-htslib=system && \
    make install

# Copy the singularity-wrapper scripts
ARG DOCKER_IMAGE
ARG DOCKER_TAG
#ENV MANIFEST_PATH="./manifest.json"
ENV DOCKER_IMAGE="$DOCKER_IMAGE"
ENV DOCKER_TAG="$DOCKER_TAG"

RUN ldconfig

COPY --from=singularity ./scripts/* /usr/local/bin/
COPY ./manifest.json /opt/wtsi-npg/etc/manifest.json

CMD ["/usr/bin/bash"]

