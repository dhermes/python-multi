from ubuntu:18.04

ENV DEBIAN_FRONTEND noninteractive

# At the moment, setting "LANG=C" on a Linux system fundamentally breaks
# Python 3. (http://bugs.python.org/issue19846)
ENV LANG C.UTF-8

# Install dependencies.
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    make \
    build-essential \
    libssl-dev \
    zlib1g-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    wget \
    curl \
    llvm \
    libncurses5-dev \
    xz-utils \
    tk-dev \
    libxml2-dev \
    libxmlsec1-dev \
    git \
    ca-certificates \
    libffi-dev \
  && apt-get clean autoclean \
  && apt-get autoremove -y \
  && rm -rf /var/lib/apt/lists/* \
  && rm -f /var/cache/apt/archives/*.deb

# Install ``pyenv``.
RUN git clone https://github.com/pyenv/pyenv /root/.pyenv

# Install the desired versions of Python, in order of
# lowest precedence to highest.
RUN for PYTHON_VERSION in 2.7.15 3.5.5 3.6.6 3.7.0 pypy2.7-6.0.0 pypy3.5-6.0.0; do \
  set -ex \
    && /root/.pyenv/bin/pyenv install ${PYTHON_VERSION} \
    && /root/.pyenv/versions/${PYTHON_VERSION}/bin/python -m pip install --upgrade pip \
  ; done

# Add to PATH, in order of lowest precedence to highest.
ENV PATH /root/.pyenv/versions/pypy3.5-6.0.0/bin:${PATH}
ENV PATH /root/.pyenv/versions/pypy2.7-6.0.0/bin:${PATH}
ENV PATH /root/.pyenv/versions/3.5.5/bin:${PATH}
ENV PATH /root/.pyenv/versions/3.7.0/bin:${PATH}
ENV PATH /root/.pyenv/versions/2.7.15/bin:${PATH}
ENV PATH /root/.pyenv/versions/3.6.6/bin:${PATH}

CMD ["python3.6"]
