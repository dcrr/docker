FROM ubuntu:16.04
MAINTAINER Diana Rojas, Exood <dianacarolinarojas@gmail.com>

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

ENV WKHTMLTOX_X64 https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.2.1/wkhtmltox-0.12.2.1_linux-trusty-amd64.deb

#--no-install-recommends
# Installing some dependencies, lessc and less-plugin-clean-css, and wkhtmltopdf
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
			libevent-dev \
			libjpeg-dev \
			python-pip 
             
#RUN set -x; \
#        curl -o wkhtmltox.deb -SL ${WKHTMLTOX_X64} \
#        && dpkg --force-depends -i wkhtmltox.deb \
#        && apt-get -y install -f --no-install-recommends \
#        && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false npm \
#        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb

# Add user and group odoo
RUN adduser --system --quiet --shell=/bin/bash --home=/home/odoo --gecos 'ODOO' --group odoo

# Creating the necessary folders to share, with the container, the odoo sources and other host files
RUN mkdir -p /home/odoo/odoo8 
RUN mkdir -p /home/odoo/share 

VOLUME ["/home/odoo/share", "/home/odoo/odoo8"]

# Set owner to /home/odoo
RUN chown -R odoo:odoo /home/odoo/*

# Wheel is necessary to compile the some dependencies of odoo
RUN pip install wheel

# Copying the odoo requirements file
COPY ./requirements.txt /home/odoo
RUN chown odoo:odoo /home/odoo/requirements.txt

# Setting the default user when running the container
USER odoo

# Installing the odoo dependencies
RUN pip install -r /home/odoo/requirements.txt

# Removing PIL to avoid errors in the excution of odoo
RUN rm -rf /home/odoo/.local/lib/python2.7/site-packages/PIL/

USER root
RUN pip install --no-cache-dir -I pillow

# Expose Odoo services
EXPOSE 8069 8071

CMD ["bin/bash"]
