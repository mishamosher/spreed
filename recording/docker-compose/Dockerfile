FROM ubuntu:20.04

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update
RUN apt-get -y upgrade

# Common dependencies
RUN apt-get -y install software-properties-common

# spreed-recording dependencies
RUN apt-get -y install ffmpeg pulseaudio python3-pip xvfb
RUN pip3 install --upgrade requests

# firefox
RUN apt-get -y install firefox firefox-geckodriver

# chromium
# The phd/chromium-browser PPA is used because since Ubuntu 20.04 Chromium is provided as a snap package
RUN add-apt-repository -y ppa:phd/chromium-browser
RUN printf "Package: *\nPin: release o=LP-PPA-phd-chromium-browser\nPin-Priority: 1001" > /etc/apt/preferences.d/phd-chromium-browser
RUN apt-get -y install chromium-browser chromium-chromedriver

# spreed-recording config
RUN useradd --create-home recording
COPY server.conf.in /etc/nextcloud-talk-recording/server.conf
RUN sed -i 's/#listen =.*/listen = 0.0.0.0:8000/' /etc/nextcloud-talk-recording/server.conf

# Deploy recording server
RUN mkdir -p /tmp/recording
COPY src /tmp/recording/
COPY pyproject.toml /tmp/recording/
RUN python3 -m pip install /tmp/recording/

# Cleanup
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
RUN rm -rf /tmp/recording

# Switch user and start the recording server
WORKDIR "/home/recording/"
USER "recording"
CMD ["python3", "-m", "nextcloud.talk.recording", "--config", "/etc/nextcloud-talk-recording/server.conf"]
