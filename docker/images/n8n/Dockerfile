FROM node:16-alpine

ARG N8N_VERSION=0.231.3 

RUN if [ -z "$N8N_VERSION" ] ; then echo "The N8N_VERSION argument is missing!" ; exit 1; fi

# Update everything and install needed dependencies
RUN apk add --update graphicsmagick tzdata git tini su-exec

# Install fonts
RUN apk --no-cache add --virtual fonts msttcorefonts-installer fontconfig && \
	  update-ms-fonts && \
	  fc-cache -f && \
	  apk del fonts && \
	  find  /usr/share/fonts/truetype/msttcorefonts/ -type l -exec unlink {} \; \
  	&& rm -rf /root /tmp/* /var/cache/apk/* && mkdir /root && \
    apk --update add --virtual build-dependencies python3 build-base ca-certificates 


# Install n8n and the packages it needs to build it correctly.
RUN npm config set python "$(which python3)" && \
	  npm_config_user=root npm install -g full-icu n8n@${N8N_VERSION} && \
	  apk del build-dependencies

ENV NODE_ICU_DATA /usr/local/lib/node_modules/full-icu

WORKDIR /data

COPY docker-entrypoint.sh /docker-entrypoint.sh
USER node
ENTRYPOINT ["tini", "--", "/docker-entrypoint.sh"]

EXPOSE 5678/tcp
