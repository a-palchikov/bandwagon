FROM quay.io/gravitational/debian-tall:buster

COPY _build/bandwagon /opt/bandwagon/
COPY web/build /opt/bandwagon/web/dist

EXPOSE 8000

ENTRYPOINT ["/opt/bandwagon/bandwagon"]
