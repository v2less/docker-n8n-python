FROM docker.pinolly.top/nikolaik/python-nodejs:python3.10-nodejs18

ARG N8N_VERSION

RUN if [ -z "$N8N_VERSION" ] ; then echo "The N8N_VERSION argument is missing!" ; exit 1; fi

RUN \
	apt-get update && \
	apt-get -y install graphicsmagick gosu git vim jq fonts-noto-cjk && \
        rm -rf /var/lib/apt/lists/* /tmp/*

# changing user `pn` to `node`
RUN usermod --login node --move-home --home /home/node pn && \
    groupmod --new-name node pn

# Install python requirements
RUN mkdir /requirements
COPY requirements.txt /requirements/requirements.txt
RUN python3 -m pip install --upgrade pip setuptools wheel && \
    python3 -m pip install -r /requirements/requirements.txt && \
    rm -rf /root/.cache/pip

# Set a custom user to not have n8n run as root
USER root

RUN npm_config_user=root npm install -g full-icu n8n@${N8N_VERSION} && \
    mkdir -p /usr/lib/node_modules/n8n && \
    cd /usr/lib/node_modules/n8n && \
    npm install n8n-nodes-python && \
    npm cache clean --force

ENV NODE_ICU_DATA=/usr/lib/node_modules/full-icu

WORKDIR /data

COPY docker-entrypoint.sh /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 5678/tcp
