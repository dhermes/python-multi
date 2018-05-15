FROM ubuntu:18.04

ENV DEBIAN_FRONTEND noninteractive

# Ensure local Python is preferred over distribution Python.
ENV PATH /usr/local/bin:$PATH

# http://bugs.python.org/issue19846
# At the moment, setting "LANG=C" on a Linux system fundamentally breaks
# Python 3.
ENV LANG C.UTF-8

# Install dependencies.
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    build-essential \
    ca-certificates \
    dirmngr \
    git \
    gpg \
    gpg-agent \
    libreadline-dev \
    libssl-dev \
    ssh \
    tcl \
    tcl-dev \
    tk \
    tk-dev \
    wget \
  && apt-get clean autoclean \
  && apt-get autoremove -y \
  && rm -rf /var/lib/apt/lists/* \
  && rm -f /var/cache/apt/archives/*.deb

# Install the desired versions of Python.
RUN for PYTHON_VERSION in 2.7.15 3.5.5 3.6.5; do \
  set -ex \
    && wget --no-check-certificate -O python-${PYTHON_VERSION}.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz" \
    && wget --no-check-certificate -O python-${PYTHON_VERSION}.tar.xz.asc "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys \
      # 2.7.15 (REF: https://github.com/docker-library/python/blob/dd864c1f145a6e1282e2e278cd8a9c649e9a8841/2.7/stretch/Dockerfile#L18)
      C01E1CAD5EA2C4F0B8E3571504C367C218ADD4FF \
      # 3.5.5 (REF: https://github.com/docker-library/python/blob/dd864c1f145a6e1282e2e278cd8a9c649e9a8841/3.5/jessie/Dockerfile#L22)
      97FC712E4C024BBEA48A61ED3A5CA953F73C700D \
      # 3.6.5 (REF: https://github.com/docker-library/python/blob/dd864c1f145a6e1282e2e278cd8a9c649e9a8841/3.6/stretch/Dockerfile#L22)
      0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D \
    && gpg --batch --verify python-${PYTHON_VERSION}.tar.xz.asc python-${PYTHON_VERSION}.tar.xz \
    && rm -r "$GNUPGHOME" python-${PYTHON_VERSION}.tar.xz.asc \
    && mkdir -p /usr/src/python-${PYTHON_VERSION} \
    && tar -xJC /usr/src/python-${PYTHON_VERSION} --strip-components=1 -f python-${PYTHON_VERSION}.tar.xz \
    && rm python-${PYTHON_VERSION}.tar.xz \
    && cd /usr/src/python-${PYTHON_VERSION} \
    && ./configure \
      --enable-shared \
      # This works only on Python 2.7 and throws a warning on every other
      # version, but seems otherwise harmless.
      --enable-unicode=ucs4 \
    && make -j$(nproc) \
    && make install \
    && ldconfig \
  ; done \
  && rm -rf /usr/src/python* \
  && rm -rf ~/.cache/

# REF: https://github.com/docker-library/pypy/blob/802584a66147dbd17a800df709124afeaa203a90/2/Dockerfile
ENV PYPY_VERSION 6.0.0
RUN set -ex; \
  \
# this "case" statement is generated via "update.sh"
    dpkgArch="$(dpkg --print-architecture)"; \
    case "${dpkgArch##*-}" in \
# amd64
    amd64) pypyArch='linux64'; sha256='6cbf942ba7c90f504d8d6a2e45d4244e3bf146c8722d64e9410b85eac6b5af67' ;; \
# arm32v5
    armel) pypyArch='linux-armel'; sha256='924ca3f90aa28e8961859508c25752c95253b842318a0f267267ffe90f56a916' ;; \
# i386
    i386) pypyArch='linux32'; sha256='ad1082d4328ae8f32617b14628648583b82b6d29df3aa42b97bd1853c08c4bc8' ;; \
    *) echo >&2 "error: current architecture ($dpkgArch) does not have a corresponding PyPy $PYPY_VERSION binary release"; exit 1 ;; \
  esac; \
  \
  wget -O pypy.tar.bz2 "https://bitbucket.org/pypy/pypy/downloads/pypy2-v${PYPY_VERSION}-${pypyArch}.tar.bz2"; \
  echo "$sha256 *pypy.tar.bz2" | sha256sum -c; \
  tar -xjC /usr/local --strip-components=1 -f pypy.tar.bz2; \
  find /usr/local/lib-python -depth -type d -a \( -name test -o -name tests \) -exec rm -rf '{}' +; \
  rm pypy.tar.bz2; \
  \
  pypy --version

# Install pip on Python 3.6 only.
# If the environment variable is called "PIP_VERSION", pip explodes with
# "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 10.0.1
RUN set -ex; \
  \
  wget -O get-pip.py 'https://bootstrap.pypa.io/get-pip.py'; \
  \
  python3.6 get-pip.py \
    --disable-pip-version-check \
    --no-cache-dir \
    "pip==$PYTHON_PIP_VERSION" \
  ; \
  pip --version; \
  \
  find /usr/local -depth \
    \( \
      \( -type d -a \( -name test -o -name tests \) \) \
      -o \
      \( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
    \) -exec rm -rf '{}' +; \
  rm -f get-pip.py

CMD ["python3.6"]
