FROM buildpack-deps:stretch

RUN groupadd --gid 1000 node \
  && useradd --uid 1000 --gid node --shell /bin/bash --create-home node

ENV ALINODE_VERSION 6.4.1
ENV ARCH=x64
ENV ENABLE_NODE_LOG YES
ENV NODE_LOG_DIR /tmp
ENV HOME /root

RUN curl -fsSLO --compressed "https://npm.taobao.org/mirrors/alinode/v$ALINODE_VERSION/alinode-v$ALINODE_VERSION-linux-$ARCH.tar.gz" \
  && curl -fsSLO --compressed "https://npm.taobao.org/mirrors/alinode/v$ALINODE_VERSION/SHASUMS256.txt" \
  && grep " alinode-v$ALINODE_VERSION-linux-$ARCH.tar.gz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -zxf "alinode-v$ALINODE_VERSION-linux-$ARCH.tar.gz" -C /usr/local --strip-components=1 --no-same-owner \
  && rm "alinode-v$ALINODE_VERSION-linux-$ARCH.tar.gz" SHASUMS256.txt \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs \
  && ENABLE_NODE_LOG=NO npm install -g @alicloud/agenthub

COPY ./default.config.js $HOME
COPY ./start-agenthub.sh /
RUN chmod u+x /start-agenthub.sh

ENV YARN_VERSION 1.22.10

RUN set -ex \
  && for key in \
    6A010C5166006599AA17F08146C2130DFD2497F5 \
  ; do \
    gpg --batch --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
    gpg --batch --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
    gpg --batch --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
  done \
  && curl -fsSLO --compressed "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz" \
  && curl -fsSLO --compressed "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz.asc" \
  && gpg --batch --verify yarn-v$YARN_VERSION.tar.gz.asc yarn-v$YARN_VERSION.tar.gz \
  && mkdir -p /opt \
  && tar -xzf yarn-v$YARN_VERSION.tar.gz -C /opt/ \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg \
  && rm yarn-v$YARN_VERSION.tar.gz.asc yarn-v$YARN_VERSION.tar.gz \
  # smoke test
  && yarn --version

ENTRYPOINT ["/start-agenthub.sh"]
