# Avoid excess privileges

# 1. Rootless containers

FROM alpine:3.12
# Create user and set ownership and permissions as required
RUN adduser -D myuser && chown -R myuser /myapp-data
# ... copy application files
USER myuser
ENTRYPOINT ["/myapp"]

# 2. Don't bind to a specific UID

# Truble
RUN mkdir /myapp-tmp-dir && chown -R myuser /myapp-tmp-dir
USER myuser
ENTRYPOINT ["/myapp"]

# Use tmp dir
USER myuser
ENV APP_TMP_DATA=/tmp
ENTRYPOINT ["/myapp"]

# 3. Make root the owner of the executable files and prevent these files from being modified

# Avoid
WORKDIR $APP_HOME
COPY --chown=app:app app-files/ /app
USER app
ENTRYPOINT /app/my-app-entrypoint.sh

# 4. Multi-stage builds

#This is the "builder" stage
FROM golang:1.15 as builder

WORKDIR /my-go-app
COPY app-src .
RUN GOOS=linux GOARCH=amd64 go build ./cmd/app-service

#This is the final stage, and we copy artifacts from "builder"
FROM gcr.io/distroless/static-debian10

COPY --from=builder /my-go-app/app-service /bin/app-service
ENTRYPOINT ["/bin/app-service"]

# 5. Distroless, from scratch
FROM ubuntu:xenial-20210114

# ❯ docker run -v /var/run/docker.sock:/var/run/docker.sock --rm quay.io/sysdig/secure-inline-scan:2 image-ubuntu -k $SYSDIG_SECURE_TOKEN --storage-type docker-daemon
# Inspecting image from Docker daemon -- distroless-1:latest
#   Full image:  docker.io/library/image-ubuntu
#   Full tag:    localbuild/distroless-1:latest
# …
# Analyzing image…
# Analysis complete!
# ...
# Evaluation results
#  - warn dockerfile:instruction Dockerfile directive 'HEALTHCHECK' not found, matching condition 'not_exists' check
#  - warn dockerfile:instruction Dockerfile directive 'USER' not found, matching condition 'not_exists' check
#  - warn files:suid_or_guid_set SUID or SGID found set on file /bin/mount. Mode: 0o104755
#  - warn files:suid_or_guid_set SUID or SGID found set on file /bin/su. Mode: 0o104755
#  - warn files:suid_or_guid_set SUID or SGID found set on file /bin/umount. Mode: 0o104755
#  - warn files:suid_or_guid_set SUID or SGID found set on file /sbin/pam_extrausers_chkpwd. Mode: 0o102755
#  - warn files:suid_or_guid_set SUID or SGID found set on file /sbin/unix_chkpwd. Mode: 0o102755
#  - warn files:suid_or_guid_set SUID or SGID found set on file /usr/bin/chage. Mode: 0o102755
# …
# Vulnerabilities report
#    Vulnerability    Severity Package                                  Type     Fix version      URL
#  - CVE-2019-18276   Low      bash-4.3-14ubuntu1.4                     dpkg     None             http://people.ubuntu.com/~ubuntu-security/cve/CVE-2019-18276
#  - CVE-2016-2781    Low      coreutils-8.25-2ubuntu3~16.04            dpkg     None             http://people.ubuntu.com/~ubuntu-security/cve/CVE-2016-2781
#  - CVE-2017-8283    Negligible dpkg-1.18.4ubuntu1.6                   dpkg     None             http://people.ubuntu.com/~ubuntu-security/cve/CVE-2017-8283
#  - CVE-2020-13844   Medium   gcc-5-base-5.4.0-6ubuntu1~16.04.12       dpkg     None             http://people.ubuntu.com/~ubuntu-security/cve/CVE-2020-13844
# ...
#  - CVE-2018-20839   Medium   systemd-sysv-229-4ubuntu21.29            dpkg     None             http://people.ubuntu.com/~ubuntu-security/cve/CVE-2018-20839
#  - CVE-2016-5011    Low      util-linux-2.27.1-6ubuntu3.10            dpkg     None             http://people.ubuntu.com/~ubuntu-security/cve/CVE-2016-5011

FROM gcr.io/distroless/base-debian10

FROM gcr.io/distroless/static-debian10

# 11. Build context and dockerignore
# docker build -t myimage .
# Beware!
COPY . /my-app
# docker build -t myimage files/

# 12. Layer order
# Not properly
FROM ubuntu
RUN apt-get install -y wget
RUN wget https://…/downloadedfile.tar
RUN tar xvzf downloadedfile.tar
RUN rm downloadedfile.tar
RUN apt-get remove wget

# Right
FROM ubuntu
RUN apt-get install wget && wget https://…/downloadedfile.tar && tar xvzf downloadedfile.tar && rm downloadedfile.tar && apt-get remove wget

# The location of the commands

# Not properly
FROM ubuntu
COPY source/* .
RUN apt-get install nodejs
ENTRYPOINT ["/usr/bin/node", "/main.js"]

# Right
FROM ubuntu
RUN apt-get install nodejs
COPY source/* .
ENTRYPOINT ["/usr/bin/node", "/main.js"]
