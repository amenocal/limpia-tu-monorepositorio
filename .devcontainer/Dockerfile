# git-sizer staging build
FROM golang:alpine AS git-sizer-build-env
RUN apk add --no-cache git
RUN go install github.com/github/git-sizer@latest

# git filter-repo and git install
FROM alpine:latest
RUN apk add --update --no-cache \
    git \
    git-lfs \
    python3 \
    py3-pip && \
    ln -sf python3 /usr/bin/python

# Create a Python virtual environment and activate it
RUN python3 -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

RUN pip3 install --no-cache --upgrade pip setuptools
RUN pip3 install git-filter-repo
WORKDIR /source
RUN git clone https://github.com/devopsdaysmedellin/monorepositorio
COPY --from=git-sizer-build-env /go/bin/git-sizer /usr/bin/git-sizer