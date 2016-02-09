FROM debian:jessie
MAINTAINER Odoo S.A. <info@openerp.com>

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            node-less \
            node-clean-css \
            python-pyinotify \
            fonts-dejavu \
            fonts-dejavu-core \
            fonts-dejavu-extra \
            unzip \
            locales-all \
            python-renderpm \
            python-support \
            locales \
        && curl -o wkhtmltox.deb -SL http://nightly.openerp.com/extra/wkhtmltox-0.12.1.2_linux-jessie-amd64.deb \
        && echo '40e8b906de658a2221b15e4e8cd82565a47d7ee8 wkhtmltox.deb' | sha1sum -c - \
        && dpkg --force-depends -i wkhtmltox.deb \
        && apt-get -y install -f --no-install-recommends \
        && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false npm \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb

# Install OpenERP
RUN set -x; \
        curl -o openerp.deb -SL http://nightly.openerp.com/7.0/nightly/deb/openerp_7.0.latest_all.deb \
        && dpkg --force-depends -i openerp.deb \
        && apt-get update \
        && apt-get -y install -f --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* openerp.deb

# Install barcode font
RUN curl http://www.reportlab.com/ftp/pfbfer.zip --output /tmp/pfbfer.zip \
        && mkdir -p /usr/lib/python2.7/dist-packages/reportlab/fonts \
        && unzip /tmp/pfbfer.zip -d /usr/lib/python2.7/dist-packages/reportlab/fonts/

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./openerp-server.conf /etc/openerp/
RUN chown openerp /etc/openerp/openerp-server.conf

RUN locale-gen en_GB.UTF-8
ENV LANG en_GB.UTF-8
ENV LANGUAGE en_GB:en
ENV LC_ALL en_GB.UTF-8

# Mount /var/lib/openerp to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/extra-addons \
        && chown -R openerp /mnt/extra-addons
VOLUME ["/var/lib/openerp", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV OPENERP_SERVER /etc/openerp/openerp-server.conf

USER openerp
ENTRYPOINT ["/entrypoint.sh"]
CMD ["openerp-server"]
