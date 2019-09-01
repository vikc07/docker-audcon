# make sure python and ffmpeg are on same debian version
FROM vikramchauhan/ffmpeg:alpine as ffmpeg

FROM python:3-alpine
MAINTAINER Vikram Chauhan <me@vkrm.ch>

ARG AUDCON_DIR="/audcon"
ARG FFMPEG_DIR="/ffmpeg"
ARG RUN_USER=audcon
ARG RUN_GROUP=audcon
ARG RUN_UID=5555
ARG RUN_GID=5555
ARG RUN_PASS=audcon123@

ENV AUDCON_DIR=${AUDCON_DIR} \
    FFMPEG_DIR=${FFMPEG_DIR} \
    RUN_USER=${RUN_USER} \
    RUN_GROUP=${RUN_GROUP} \
    RUN_UID=${RUN_UID} \
    RUN_GID=${RUN_GID} \
    LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$FFMPEG_DIR/lib"
ENV PATH="$FFMPEG_DIR:$PATH"

COPY --from=ffmpeg $FFMPEG_DIR $FFMPEG_DIR

ARG DEPS="ca-certificates \
        wget \
        unzip\
        git"

RUN addgroup -g $RUN_GID $RUN_GROUP \
    && adduser -G $RUN_GROUP -u $RUN_UID -D $RUN_USER \
    && chmod -R 755 $FFMPEG_DIR \
    && apk add --no-cache --update ${DEPS}\
    && cd / \
    && wget https://github.com/vikc07/audcon/archive/master.zip \
    && unzip master.zip \
    && mv audcon-master $AUDCON_DIR \
    && rm master.zip \
    && pip install -r $AUDCON_DIR/requirements.txt \
    && apk del ${DEPS} \
    && rm -rf /var/cache/apk/* \
    && mkdir /temp \
    && chown -R $RUN_USER:$RUN_GROUP $AUDCON_DIR \
    && chmod -R 755 $AUDCON_DIR \
    && chown -R $RUN_USER:$RUN_GROUP /temp \
    && cp -p $AUDCON_DIR/run_alpine.sh /run.sh

USER $RUN_USER

# Set entrypoint
ENTRYPOINT ["/run.sh"]