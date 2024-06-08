# Use the official Node.js image as a base
ARG NODE_VERSION=20
FROM n8nio/base:${NODE_VERSION}

# Set the N8N version
ARG N8N_VERSION=1.44.1
RUN if [ -z "$N8N_VERSION" ] ; then echo "The N8N_VERSION argument is missing!" ; exit 1; fi

ENV N8N_VERSION=${N8N_VERSION}
ENV NODE_ENV=production
ENV N8N_RELEASE_TYPE=stable

# Install n8n and dependencies
RUN set -eux; \
	npm install -g --omit=dev n8n@${N8N_VERSION} --ignore-scripts && \
	npm rebuild --prefix=/usr/local/lib/node_modules/n8n sqlite3 && \
	rm -rf /usr/local/lib/node_modules/n8n/node_modules/@n8n/chat && \
	rm -rf /usr/local/lib/node_modules/n8n/node_modules/n8n-design-system && \
	rm -rf /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/node_modules && \
	find /usr/local/lib/node_modules/n8n -type f -name "*.ts" -o -name "*.js.map" -o -name "*.vue" | xargs rm -f && \
	rm -rf /root/.npm

# Copy the entrypoint script
COPY docker-entrypoint.sh /

# Create the .n8n directory and set permissions
RUN \
	mkdir .n8n && \
	chown node:node .n8n

# Set the shell
ENV SHELL /bin/sh

# Use the node user
USER node

# Set the entrypoint
ENTRYPOINT ["tini", "--", "/docker-entrypoint.sh"]
