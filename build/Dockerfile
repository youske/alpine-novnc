# alpine linux novnc

FROM alpine:latest
MAINTAINER youske miyakoshi <youske@gmail.com>

# s6-overlay
ENV S6OVERLAY_VERSION="v1.11.0.1"
ENV S6OVERLAY_URL="https://github.com/just-containers/s6-overlay/releases/download/${S6OVERLAY_VERSION}/s6-overlay-amd64.tar.gz"
ADD ${S6OVERLAY_URL} /tmp/

ENV ENTRYKIT_VERSION=0.4.0
ENV ENTRYKIT_FILE=entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz
ENV ENTRYKIT_DOWNLOAD=https://github.com/progrium/entrykit/releases/download/v${ENTRYKIT_VERSION}/${ENTRYKIT_FILE}
ADD ${ENTRYKIT_DOWNLOAD} /tmp/

ENV GOSU_VERSION=1.7
ENV GOSU_DOWNLOAD=https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-amd64
ADD ${GOSU_DOWNLOAD} /usr/bin/

# Set correct environment variables.
ENV HOME=/root
ENV PORT=8080
ENV DISPLAYSIZE=${XDISPLAY_WIDTH}"x"${XDISPLAY_WIDTH}"x"${XDISPLAY_DEPTH}
ENV DEBIAN_FRONTEND=noninteractive

ENV LC_ALL=C.UTF-8
ENV LANG=ja_JP.UTF-8
ENV LANGUAGE=${LANG}

# timezone settings
ENV TZ=Asia/Tokyo
RUN apk --update add tzdata --no-cache && \
    cp /usr/share/zoneinfo/${TZ} /etc/localtime && echo $TZ > /etc/timezone && \
    apk del tzdata

ENV PACKAGE="wget ca-certificates git bash python xvfb x11vnc fluxbox net-tools socat supervisor xterm "
# mozc-server fonts-takao-gothic fonts-takao-mincho

# Installing apps and clean up.
RUN echo "http://dl-3.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
RUN apk update upgrade --no-cache
RUN apk add --no-cache ${PACKAGE}

RUN git clone https://github.com/kanaka/noVNC.git /opt/noVNC && \
    git clone https://github.com/kanaka/websockify /opt/websockify && \
    ln -sf /opt/websockify /opt/noVNC/utils/websockify && \
    rm -rf /opt/noVNC/.git && \
    rm -rf /opt/websockify/.git

# Download and install noVNC.
#    wget -qO- "https://github.com/kanaka/noVNC/tarball/master" | tar zx -C /opt/noVNC/ && \
#    wget -qO- "https://github.com/kanaka/websockify/tarball/master" | tar zx -C /opt/noVNC/utils/websockify/ && \
RUN    ln -s /opt/noVNC/vnc.html /opt/noVNC/index.html


# entrykit install
RUN tar zxf /tmp/${ENTRYKIT_FILE} -C /usr/bin && \
    chmod +x /usr/bin/entrykit && /usr/bin/entrykit --symlink

# gosu
RUN chmod +x /usr/bin/gosu-amd64 && ln -s /usr/bin/gosu-amd64 /usr/bin/gosu

# s6-overlay
RUN tar zxf /tmp/s6-overlay-amd64.tar.gz -C /

# chromium browser
#RUN apt-get install -y chromium-browser

#RUN sed -i -- "s/ps -p/ps -o pid | grep/g" /opt/noVNC/utils/launch.sh

# Configure & run supervisor
EXPOSE ${PORT} 6000
COPY novnc.conf /etc/supervisor/conf.d/novnc.conf
#ENTRYPOINT ["render","/etc/supervisor/conf.d/novnc.conf","--","switch","shell=/bin/bash","--","/init"]
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/novnc.conf"]
