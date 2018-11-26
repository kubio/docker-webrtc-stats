FROM node:8.12-stretch

# see https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md#running-puppeteer-in-docker
RUN apt-get update && \
    apt-get -y install libgconf-2-4\
    expect \
    git \
    sudo \
    libnss3 \
    python-pip \
    python-lxml \
    libwww-perl \
    libcss-dom-perl

RUN apt-get update && apt-get install -y wget --no-install-recommends \
    && wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list' \
    && apt-get update \
    && apt-get install -y google-chrome-unstable fonts-ipafont-gothic fonts-wqy-zenhei fonts-thai-tlwg fonts-kacst ttf-freefont \
      --no-install-recommends \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge --auto-remove -y curl \
    && rm -rf /src/*.deb

# install cmake
WORKDIR /tmp
RUN wget https://github.com/Kitware/CMake/releases/download/v3.13.0/cmake-3.13.0.tar.gz && \
    tar xvf cmake-3.13.0.tar.gz
WORKDIR /tmp/cmake-3.13.0
RUN ./bootstrap && make && make install

# setup webrtc-stats
RUN git clone https://github.com/w3c/webrtc-stats.git /app
WORKDIR /app

RUN pip install virtualenv html5lib
RUN npm -g install colors

RUN make clean
RUN expect -c "\
    set timeout 20;\
    spawn make setup;\
    expect \"Do you want to continue?\"; send \"yes\r\";\
    expect eof;\
    "
RUN make update
RUN make check