FROM ubuntu:18.04 as samtools_build
ARG SAMTOOLS_RELEASE=1.9

LABEL description="Build container for Samtools"

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    bzip2 \
    ca-certificates \
    curl \
    libbz2-dev \
    libncurses5-dev \
    liblzma-dev \
    zlib1g-dev

RUN curl -L -o /tmp/samtools-${SAMTOOLS_RELEASE}.tar.bz2 https://github.com/samtools/samtools/releases/download/${SAMTOOLS_RELEASE}/samtools-${SAMTOOLS_RELEASE}.tar.bz2
RUN tar -xvjf /tmp/samtools-${SAMTOOLS_RELEASE}.tar.bz2 -C /tmp
WORKDIR /tmp/samtools-${SAMTOOLS_RELEASE}
RUN ./configure --prefix=/tmp/samtools
RUN make -j5
RUN make install


FROM ubuntu:18.04 as bcftools_build
ARG BCFTOOLS_RELEASE=1.9

LABEL description="Build container for BCFtools"

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    bzip2 \
    ca-certificates \
    curl \
    libbz2-dev \
    liblzma-dev \
    zlib1g-dev

RUN curl -L -o /tmp/bcftools-${BCFTOOLS_RELEASE}.tar.bz2 https://github.com/samtools/bcftools/releases/download/${BCFTOOLS_RELEASE}/bcftools-${BCFTOOLS_RELEASE}.tar.bz2
RUN tar -xvjf /tmp/bcftools-${BCFTOOLS_RELEASE}.tar.bz2 -C /tmp
WORKDIR /tmp/bcftools-${BCFTOOLS_RELEASE}
RUN ./configure --prefix=/tmp/bcftools
RUN make -j5
RUN make install


FROM ubuntu:18.04 as htslib_build
ARG HTSLIB_RELEASE=1.9

LABEL description="Build container for HTSlib"

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    bzip2 \
    ca-certificates \
    curl \
    libbz2-dev \
    liblzma-dev \
    zlib1g-dev

RUN curl -L -o /tmp/htslib-${HTSLIB_RELEASE}.tar.bz2 https://github.com/samtools/htslib/releases/download/${HTSLIB_RELEASE}/htslib-${HTSLIB_RELEASE}.tar.bz2
RUN tar -xvjf /tmp/htslib-${HTSLIB_RELEASE}.tar.bz2 -C /tmp
WORKDIR /tmp/htslib-${HTSLIB_RELEASE}
RUN ./configure --prefix=/tmp/htslib
RUN make -j5
RUN make install


FROM ubuntu:18.04 as fusera_build
ARG FUSERA_RELEASE=v1.0.0

LABEL description="Build container for Fusera"

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl

RUN curl -L -o /usr/local/bin/fusera https://github.com/mitre/fusera/releases/download/${FUSERA_RELEASE}/fusera
RUN curl -L -o /usr/local/bin/sracp https://github.com/mitre/fusera/releases/download/${FUSERA_RELEASE}/sracp


FROM ubuntu:18.04 as runtime
ARG VERSION=1.0-minimal

LABEL version=${VERSION}
LABEL description="A pre-made machine image with a set of commonly used tools"
LABEL maintainer="pleiness@umich.edu"

RUN apt-get update && apt-get install -y --no-install-recommends \
    bzip2 \
    curl \
    fuse \
    libcurl4-gnutls-dev \
    liblzma5 \
    libncurses5 \
    gzip \
    tcsh \
    wget \
    xz-utils \
    zlib1g \
    zstd \
&& rm -rf /var/lib/apt/lists/*

COPY --from=fusera_build /usr/local/bin/fusera /usr/local/bin/sracp /usr/local/bin/
RUN chmod +x /usr/local/bin/fusera /usr/local/bin/sracp

RUN useradd -d /home/docker -m -s /bin/bash docker && passwd -d docker

COPY --from=htslib_build /tmp/htslib/bin/* /usr/local/bin/
COPY --from=htslib_build /tmp/htslib/lib/* /usr/local/lib/
COPY --from=htslib_build /tmp/htslib/include/* /usr/local/include/
COPY --from=htslib_build /tmp/htslib/share/* /usr/local/share/

COPY --from=bcftools_build /tmp/bcftools/bin/* /usr/local/bin/
COPY --from=bcftools_build /tmp/bcftools/libexec /usr/local/
COPY --from=bcftools_build /tmp/bcftools/share/* /usr/local/share/

COPY --from=samtools_build /tmp/samtools/share/* /usr/local/share/
COPY --from=samtools_build /tmp/samtools/bin/* /usr/local/bin/
COPY --from=samtools_build /tmp/samtools/share/* /usr/local/share/

WORKDIR /home/docker
USER docker

