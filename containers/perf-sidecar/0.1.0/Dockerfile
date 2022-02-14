FROM docker.io/library/rockylinux:8

RUN dnf -y update
RUN dnf -y install perf

ADD entrypoint.sh /
ENTRYPOINT ["/entrypoint.sh"]
