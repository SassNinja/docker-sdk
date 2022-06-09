# syntax = docker/dockerfile:experimental
ARG SPRYKER_PARENT_IMAGE

FROM ${SPRYKER_PARENT_IMAGE} as cli-basic

# Blackfire client
RUN mkdir -p /tmp/blackfire \
    && architecture=$(case $(uname -m) in i386 | i686 | x86) echo "i386" ;; x86_64 | amd64) echo "amd64" ;; aarch64 | arm64 | armv8) echo "arm64" ;; *) echo "amd64" ;; esac) \
    && curl -A "Docker" -L https://blackfire.io/api/v1/releases/cli/linux/$architecture | tar zxp -C /tmp/blackfire \
    && mv /tmp/blackfire/blackfire /usr/bin/blackfire \
    && rm -Rf /tmp/blackfire

ENV PATH=/data/vendor/bin:$PATH

# Node.js Binary Distribution from NodeSource
# https://github.com/nodesource/distributions/blob/master/README.md
RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash

RUN --mount=type=cache,id=aptlib,sharing=locked,target=/var/lib/apt \
    --mount=type=cache,id=aptcache,sharing=locked,target=/var/cache/apt \
  bash -c 'if [ ! -z "$(which apt)" ]; then apt update -y && apt install -y \
     nodejs \
     inotify-tools \
     netcat-openbsd \
     git \
     redis-tools \
     ; fi'

# TODO Not-available feature: autoload-cache. Should be switchable
#RUN --mount=type=cache,id=composer,sharing=locked,target=/home/spryker/.composer/cache \
#    chown spryker:spryker /home/spryker/.composer/cache && chmod 0777 /home/spryker/.composer/cache
#USER spryker
#RUN --mount=type=cache,id=composer,sharing=locked,target=/home/spryker/.composer/cache \
#    composer global require sprymiker/autoload-cache:0.3.4
#USER root
#RUN rm -rf /home/spryker/.composer/cache
#
#RUN --mount=type=cache,id=npm,sharing=locked,target=/root/.npm \
#    npm install autoload-cache@0.3.4 -g

USER spryker

RUN mkdir -p /home/spryker/env
COPY --chown=spryker:spryker cli /home/spryker/bin
RUN find /home/spryker/bin -type f -exec chmod +x {} \;
ENV PATH=/home/spryker/bin:$PATH

RUN mkdir -p /home/spryker/ssh-relay/ && chmod 777 /home/spryker/ssh-relay && touch /home/spryker/ssh-relay/ssh-auth.sock && chmod 666 /home/spryker/ssh-relay/ssh-auth.sock

RUN mkdir -p /home/spryker/history && touch /home/spryker/history/.bash_history && chmod 0600 /home/spryker/history/.bash_history
ENV HISTFILE=/home/spryker/history/.bash_history

ENV NEWRELIC_ENABLED=0
