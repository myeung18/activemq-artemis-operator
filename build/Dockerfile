FROM golang:1.11.5-alpine3.8 AS build-env

ENV OPERATOR=/activemq-artemis-operator/activemq-artemis-operator \
    CGO_ENABLED=0 \
    GOPATH=/go

# Allow Go to retrieve the dependencies for the build step
RUN apk add --no-cache git

RUN mkdir -p ${GOPATH}/src/github.com/rh-messaging/activemq-artemis-operator/bin
RUN mkdir -p /activemq-artemis-operator
ADD . ${GOPATH}/src/github.com/rh-messaging/activemq-artemis-operator
WORKDIR ${GOPATH}/src/github.com/rh-messaging/activemq-artemis-operator
RUN go build -v -o ${OPERATOR} ${GOPATH}/src/github.com/rh-messaging/activemq-artemis-operator/cmd/manager

FROM alpine:3.8

ENV OPERATOR=/activemq-artemis-operator/activemq-artemis-operator \
    USER_UID=1001 \
    USER_NAME=activemq-artemis-operator \
    CGO_ENABLED=0 \
    GOPATH=/go
ENV HOME=/home/${USER_NAME}

COPY --from=build-env ${OPERATOR} /activemq-artemis-operator/
COPY --from=build-env ${GOPATH}/src/github.com/rh-messaging/activemq-artemis-operator/build/bin /usr/local/bin

RUN mkdir -p ${HOME} && \
    chown -R ${USER_UID}:0 ${HOME} && \
    chmod -R g=u ${HOME} /etc/passwd
USER ${USER_UID}
WORKDIR ${HOME}

ENTRYPOINT ["/usr/local/bin/entrypoint"]