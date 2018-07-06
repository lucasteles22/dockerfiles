FROM ruby:2.5.1

# Enviroment (bundler, gem, directory paths and PATH)
ENV APP_HOME=/app \
    GEM_HOME=/home/app/gems \
    BUNDLE_PATH=/home/app/gems \
    BUNDLE_BIN=/home/app/gems/bin \
    BUNDLE_APP_CONFIG=/home/app/gems \
    PATH=/home/app/gems/bin:$PATH

RUN apt-get update -qq && \
    apt-get install -y build-essential \
    libssl-dev \
    libpq-dev \
    apt-transport-https \
    imagemagick

RUN set -ex \
  ; apt-get update \
  ; curl -o nodejs.deb https://deb.nodesource.com/node_8.x/pool/main/n/nodejs/nodejs_8.7.0-1nodesource1_amd64.deb \
  ; apt-get install -y ./nodejs.deb \
  ; rm nodejs.deb \
  ; rm -rf /var/lib/apt/lists/*

RUN apt-get update && \
      apt-get -y install sudo
RUN adduser --disabled-password --gecos '' docker
RUN adduser docker sudo
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
USER docker

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

USER root 
RUN apt-get update && apt-get install yarn

# setup basic environment to run as normal user and use /gems/bin
RUN set -ex \
    && mkdir -p $APP_HOME $BUNDLE_BIN \
    && curl -sL https://github.com/tianon/gosu/releases/download/1.10/gosu-amd64 -o /usr/local/bin/gosu \
    && sed -i s@'PATH="\(.*\)"'@'PATH="/home/app/gems/bin:\1"'@g /etc/profile \
    && gem update --system 1>/dev/null

WORKDIR $APP_HOME

EXPOSE 3000
CMD ["/bin/sh", "rails", "server", "-b", "0.0.0.0", "irb"]
CMD puma -C config/puma.rb
