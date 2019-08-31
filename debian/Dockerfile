FROM vikramchauhan/ffmpeg:latest as ffmpeg

FROM python:3-slim
MAINTAINER Vikram Chauhan <me@vkrm.ch>

ARG LD_LIBRARY_PATH="/ffmpeg/lib"
ARG DEPS="ca-certificates \
        wget \
        unzip\
        git"
ARG FFMPEG_DIR="/ffmpeg"
ARG AUDCON_DIR="/audcon"
ARG RUN_USER=audcon
ARG RUN_GROUP=audcon
ARG RUN_UID=5555
ARG RUN_GID=5555
ARG RUN_PASS=audcon123@

ENV RUN_USER=${RUN_USER} \
    RUN_GROUP=${RUN_GROUP} \
    RUN_UID=${RUN_UID} \
    RUN_GID=${RUN_GID} \
    LD_LIBRARY_PATH="$FFMPEG_DIR/lib" \
    PATH="$FFMPEG_DIR:$LD_LIBRARY_PATH:$PATH"

COPY --from=ffmpeg /ffmpeg /ffmpeg

RUN groupadd -g $RUN_GID $RUN_GROUP \
    && useradd -m $RUN_GROUP -g $RUN_USER -u $RUN_UID -p $RUN_PASS \
    && chown -R $RUN_USER:$RUN_GROUP $FFMPEG_DIR \
    && chmod -R 755 $FFMPEG_DIR \
    && apt-get -y update \
    && apt-get -y install --no-install-recommends ${DEPS} \
    && cd / \
    && wget https://github.com/vikc07/audcon/archive/master.zip \
    && unzip master.zip \
    && mv audcon-master $AUDCON_DIR \
    && rm master.zip \
    && pip install -r $AUDCON_DIR/requirements.txt \
    && apt-get -y remove ${DEPS} \
    && apt-get -y clean \
    && apt-get -y autoclean \
    && apt-get -y autoremove \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir /temp \
    && chown -R $RUN_USER:$RUN_GROUP $AUDCON_DIR \
    && chmod -R 755 $AUDCON_DIR \
    && chown -R $RUN_USER:$RUN_GROUP /temp \
    && cp -p $AUDCON_DIR/run.sh /

USER $RUN_USER

# Set entrypoint
ENTRYPOINT ["/run.sh"]