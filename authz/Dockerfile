FROM openjdk:8-alpine

# Port bindings
ENV kangaroo.port=8080
ENV kangaroo.host=0.0.0.0

# Volume construction
CMD mkdir -p /var/lib/kangaroo
VOLUME /var/lib/kangaroo

# Runtime environment
EXPOSE 8080
WORKDIR /root
CMD ["java", "-jar", "/root/kangaroo-server-authz.jar"]

# Executable
ARG version=1.2.0
ADD https://repo.maven.apache.org/maven2/net/krotscheck/kangaroo-server-authz/${version}/kangaroo-server-authz-${version}-jar-with-dependencies.jar kangaroo-server-authz.jar
