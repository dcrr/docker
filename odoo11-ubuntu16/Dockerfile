FROM ubuntu:16.04
MAINTAINER Diana Rojas, Exood <dianacarolinarojas@gmail.com>

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

ENV VERSION_ODOO 11

ENV WKHTMLTOX_X64 https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.2.1/wkhtmltox-0.12.2.1_linux-trusty-amd64.deb

# Installing some dependencies, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
			npm \
			postgresql-client \
			postgresql-client-common \
            node-less \
			python3 \
			python3-dev \
            python3-pip \
            python3-setuptools \
			python3-pyinotify \
            python3-renderpm \
			build-essential \
			libsasl2-dev \
			libldap2-dev  
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
RUN chown -R odoo:odoo /home/odoo

# Wheel is necessary to compile the some dependencies of odoo
RUN pip3 install wheel

# Installing ptvsd for debugging with visual studio code (only works with version 3.0.0)
RUN pip3 install ptvsd==3.0.0

# Copying the odoo requirements file
COPY ./requirements.txt /home/odoo
RUN chown odoo:odoo /home/odoo/requirements.txt

# Installing the odoo dependencies
RUN pip3 install -r /home/odoo/requirements.txt

# Declaring the environment variables that contain the necessary routes 
# so that odoo can be started from the entrypoint file.
ENV ODOO_EXEC /home/odoo/odoo${VERSION_ODOO}/odoo/odoo-bin
ENV ODOO_CONF /home/odoo/odoo${VERSION_ODOO}/config/odoo${VERSION_ODOO}.conf

# The entrypoint file contains the script needed to run odoo automatically 
# when the container starts
COPY ./entrypoint.sh /
RUN chmod 777 /entrypoint.sh 

# Setting the default user when running the container
USER odoo

# Expose odoo services
EXPOSE 8060 8061

# Expose the debug port
EXPOSE 4000

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]
