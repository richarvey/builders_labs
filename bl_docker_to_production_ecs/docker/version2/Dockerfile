FROM debian:latest
MAINTAINER Ric Harvey <rjh@amazon.com>

RUN apt-get update && \
    apt-get install -y curl gnupg git bash && \
    curl -sL https://deb.nodesource.com/setup_8.x | bash - && \
    apt-get install -y nodejs

RUN git clone https://github.com/os-js/OS.js.git && cd OS.js

WORKDIR OS.js

RUN npm install 
RUN node osjs build

RUN apt-get install -y build-essential
RUN ./bin/add-package-repo.sh games https://github.com/os-js/OS.js-games.git

CMD node osjs run
