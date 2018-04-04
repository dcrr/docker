FROM ubuntu:16.04
MAINTAINER Diana Rojas, Exood <dianacarolinarojas@gmail.com>

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

ENV VERSION_ODOO 9

ENV WKHTMLTOX_X64 https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.2.1/wkhtmltox-0.12.2.1_linux-trusty-amd64.deb

# Installing some dependencies, lessc and less-plugin-clean-css, and wkhtmltopdf
#--no-install-recommends it was commented because it generated error when downloading libsasl2-dev
RUN set -x; \
        apt-get update \
        && apt-get install -y \ 
			npm \
			curl \
			postgresql-client \
			postgresql-client-common \
            node-less \
			python \
			python-dev \
            build-essential \
            python-setuptools \
            libsasl2-dev \
			libldap2-dev \
			libxslt1-dev \
			libxml2-dev \
			zlib1g-dev \
			libevent-dev \
			libjpeg-dev \
			python-pip 
             
RUN set -x; \
        curl -o wkhtmltox.deb -SL ${WKHTMLTOX_X64} \
        && dpkg --force-depends -i wkhtmltox.deb \
        && apt-get -y install -f --no-install-recommends \
        && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false npm \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb

# Add user and group odoo
RUN adduser --system --quiet --shell=/bin/bash --home=/home/odoo --gecos 'ODOO' --group odoo

# Creating the necessary folders to share, with the container, the odoo sources and other host files
RUN mkdir -p /home/odoo/odoo${VERSION_ODOO}
RUN mkdir -p /home/odoo/share 

VOLUME ["/home/odoo/share", "/home/odoo/odoo${VERSION_ODOO}"]

# Set owner to /home/odoo
RUN chown -R odoo:odoo /home/odoo/*

RUN pip install --upgrade pip

# Copying the odoo requirements file
COPY ./requirements.txt /home/odoo
RUN chown odoo:odoo /home/odoo/requirements.txt

# Installing the odoo dependencies
RUN pip install -r /home/odoo/requirements.txt

# Removing PIL to avoid errors in the excution of odoo
RUN rm -rf //usr/local/lib/python2.7/dist-packages/PIL/
RUN pip install --no-cache-dir -I pillow

# Installing ptvsd for debugging with visual studio code (only works with version 3.0.0)
RUN pip install ptvsd==3.0.0

# Setting the default user when running the container
USER odoo

# Expose odoo services
EXPOSE 8080 8081

# Expose the debug port
EXPOSE 3000

CMD ["bin/bash"]
