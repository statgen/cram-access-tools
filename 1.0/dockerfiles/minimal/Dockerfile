FROM ubuntu:18.04 as fusera_build

ARG FUSERA_RELEASE=v1.0.0

LABEL description="Build container for Fusera"

RUN apt-get update && apt-get install -y \
    ca-certificates \
    curl

RUN curl -L -o /usr/local/bin/fusera https://github.com/mitre/fusera/releases/download/${FUSERA_RELEASE}/fusera
RUN curl -L -o /usr/local/bin/sracp https://github.com/mitre/fusera/releases/download/${FUSERA_RELEASE}/sracp


FROM ubuntu:18.04 as runtime

ARG BCFTOOLS_RELEASE=1.9
ARG HTSLIB_RELEASE=1.9
ARG SAMTOOLS_RELEASE=1.9
ARG VERSION=1.0-minimal

LABEL version=${VERSION}
LABEL description="A Docker image with a set of commonly used CRAM tools"
LABEL maintainer="pleiness@umich.edu"

RUN apt-get update && apt-get install -y \
    build-essential \
    bzip2 \
    ca-certificates \
    curl \
    fuse \
    gzip \
    libbz2-dev \
    libcrypto++-dev \
    libcrypto++6 \
    libcurl4 \
    libcurl4-openssl-dev \
    liblzma-dev \
    liblzma5 \
    libncurses5 \
    libncurses5-dev \
    libssl-dev \
    tcsh \
    wget \
    xz-utils \
    zlib1g \
    zlib1g-dev \
    zstd \
&& rm -rf /var/lib/apt/lists/*

COPY --from=fusera_build /usr/local/bin/fusera /usr/local/bin/sracp /usr/local/bin/
RUN chmod +x /usr/local/bin/fusera /usr/local/bin/sracp

RUN useradd -d /home/docker -m -s /bin/bash docker && passwd -d docker

# Install htslib
RUN curl -L -o /tmp/htslib-${HTSLIB_RELEASE}.tar.bz2 https://github.com/samtools/htslib/releases/download/${HTSLIB_RELEASE}/htslib-${HTSLIB_RELEASE}.tar.bz2 \
    && tar -xvjf /tmp/htslib-${HTSLIB_RELEASE}.tar.bz2 -C /tmp \
    && rm /tmp/htslib-${HTSLIB_RELEASE}.tar.bz2
WORKDIR /tmp/htslib-${HTSLIB_RELEASE}

RUN ./configure --enable-libcurl \
    && make \
    && make install

# Install samtools
RUN curl -L -o /tmp/samtools-${SAMTOOLS_RELEASE}.tar.bz2 https://github.com/samtools/samtools/releases/download/${SAMTOOLS_RELEASE}/samtools-${SAMTOOLS_RELEASE}.tar.bz2 \
    && tar -xvjf /tmp/samtools-${SAMTOOLS_RELEASE}.tar.bz2 -C /tmp \
    && rm /tmp/samtools-${SAMTOOLS_RELEASE}.tar.bz2
WORKDIR /tmp/samtools-${SAMTOOLS_RELEASE}
RUN ./configure --with-htslib=system \
    && make \
    && make install \
    && ldconfig

# Install bcftools
RUN curl -L -o /tmp/bcftools-${BCFTOOLS_RELEASE}.tar.bz2 https://github.com/samtools/bcftools/releases/download/${BCFTOOLS_RELEASE}/bcftools-${BCFTOOLS_RELEASE}.tar.bz2 \
    && tar -xvjf /tmp/bcftools-${BCFTOOLS_RELEASE}.tar.bz2 -C /tmp \
    && rm /tmp/bcftools-${BCFTOOLS_RELEASE}.tar.bz2
WORKDIR /tmp/bcftools-${BCFTOOLS_RELEASE}
RUN ./configure \
    && make \
    && make install

# Remove /tmp files. Doesn't help size, but they don't need to be there.
RUN rm -rf /tmp/*

WORKDIR /home/docker
USER docker

