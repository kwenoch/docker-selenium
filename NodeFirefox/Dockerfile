FROM ghcr.io/kwenoch/selenium-node-base:3-aarch64
LABEL authors=SeleniumHQ

USER root

#=========
# Firefox
#=========
ARG FIREFOX_VERSION="113.0.2"
RUN FIREFOX_DOWNLOAD_URL="https://github.com/kwenoch/firefox-lfs-backup/raw/refs/heads/v113/firefox_$FIREFOX_VERSION+build1-0ubuntu0.18.04.1_arm64.tar.xz" \
  && apt-get update -qqy \
  && apt-get -qqy --no-install-recommends install firefox xz-utils \
  && rm -rf /var/lib/apt/lists/* /var/cache/apt/* \
  && wget --no-verbose -O /tmp/firefox.tar.xz $FIREFOX_DOWNLOAD_URL \
  && apt-get -y purge firefox \
  && rm -rf /opt/firefox \
  && mkdir -p /opt/firefox \
  && tar -C /opt/firefox -xvf /tmp/firefox.tar.xz \
  && rm /tmp/firefox.tar.xz \
  && mv /opt/firefox/usr/lib/firefox /opt/firefox-$FIREFOX_VERSION \
  && rm -rf /opt/firefox \
  && ln -fs /opt/firefox-$FIREFOX_VERSION/firefox /usr/bin/firefox

#============
# GeckoDriver
#============
ARG GECKODRIVER_VERSION="0.33.0"
RUN GK_VERSION=$(if [ ${GECKODRIVER_VERSION:-latest} = "latest" ]; then echo "0.33.0"; else echo $GECKODRIVER_VERSION; fi) \
  && echo "Using GeckoDriver version: "$GK_VERSION \
  && wget --no-verbose -O /tmp/geckodriver.tar.gz https://github.com/mozilla/geckodriver/releases/download/v$GK_VERSION/geckodriver-v$GK_VERSION-linux-aarch64.tar.gz \
  && rm -rf /opt/geckodriver \
  && tar -C /opt -zxf /tmp/geckodriver.tar.gz \
  && rm /tmp/geckodriver.tar.gz \
  && mv /opt/geckodriver /opt/geckodriver-$GK_VERSION \
  && chmod 755 /opt/geckodriver-$GK_VERSION \
  && ln -fs /opt/geckodriver-$GK_VERSION /usr/bin/geckodriver

USER seluser

COPY generate_config /opt/bin/generate_config

# Generating a default config during build time
RUN /opt/bin/generate_config > /opt/selenium/config.json

#==========
# Relaxing permissions for OpenShift and other non-sudo environments
#==========
RUN sudo chmod -R 777 ${HOME} \
  && sudo chgrp -R 0 ${HOME} \
  && sudo chmod -R g=u ${HOME}
