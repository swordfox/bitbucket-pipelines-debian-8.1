FROM php:8.0-alpine

ARG BUILD_DATE
ARG VCS_REF

LABEL maintainer="Dan Pock <self@danpock.me>" \
    PHP="8.0" \
    NODE="18" \
    IMAGE_VERSION=$IMAGE_VERSION \
    org.label-schema.name="swordfox/bitbucket-pipelines-debian-8.1" \
    org.label-schema.description=":coffee: Docker images for build and test PHP applications with Gitlab CI (or any other CI plataform!)" \
    org.label-schema.build-date=$BUILD_DATE \
    org.label-schema.schema-version="1.0" \
    org.label-schema.vcs-url="https://github.com/swordfox/bitbucket-pipelines-debian-8.1" \
    org.label-schema.vcs-ref=$VCS_REF

# Set correct environment variables
ENV IMAGE_USER=php
ENV IMAGE_VERSION=$IMAGE_VERSION
ENV HOME=/home/$IMAGE_USER
ENV COMPOSER_HOME=$HOME/.composer
ENV PATH=$HOME/.yarn/bin:$PATH
ENV GOSS_VERSION="0.3.16"
ENV NODE_VERSION=18
ENV NPM_VERSION=8
ENV YARN_VERSION=latest
ENV PHP_VERSION=8.0

WORKDIR /tmp

COPY ./php/scripts/alpine/*.sh /tmp/
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
COPY --from=mhart/alpine-node:18 /usr/bin/node /usr/bin/
COPY --from=mhart/alpine-node:18 /usr/lib/libgcc* /usr/lib/libstdc* /usr/lib/* /usr/lib/

# COPY INSTALL SCRIPTS
RUN apk update --no-cache \
  && chmod +x /tmp/*.sh \
  && adduser -D $IMAGE_USER \
  && mkdir -p /var/www/html \
  && apk add --update --no-cache bash \
  && bash ./packages.sh \
  && bash ./extensions.sh \
  && bash ./nodeyarn.sh \
  && rm -rf ~/.composer/cache/* \
  && chown -R $IMAGE_USER:$IMAGE_USER /var/www $HOME \
  && echo "$IMAGE_USER  ALL = ( ALL ) NOPASSWD: ALL" >> /etc/sudoers \
  && curl -fsSL https://goss.rocks/install | GOSS_VER=v${GOSS_VERSION} sh \
  && bash ./cleanup.sh

USER $IMAGE_USER

WORKDIR /var/www/html

CMD ["php", "-a"]
