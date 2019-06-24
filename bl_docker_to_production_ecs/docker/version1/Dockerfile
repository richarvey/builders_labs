FROM debian:latest
MAINTAINER Ric Harvey <rjh@amazon.com>

RUN apt-get update && \
    apt-get install -y curl gnupg git && \
    curl -sL https://deb.nodesource.com/setup_8.x | bash - && \
    apt-get install -y nodejs

RUN git clone https://github.com/os-js/OS.js.git && cd OS.js

WORKDIR OS.js

RUN npm install 
run node osjs build

CMD node osjs run
