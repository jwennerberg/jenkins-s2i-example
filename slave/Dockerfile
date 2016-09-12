# The FROM will be replaced when building in OpenShift
FROM rhel7/rhel

MAINTAINER Red Hat Consulting

ENV BASE_URL="http://192.168.121.1/" \
  JDK_PKG=ibm-java-sdk-8.0-3.11-x86_64-archive.bin \
  JDK_VERSION=1.8.0_sr3fp11 \
  JAVA_HOME=/opt/ibm/java

# Install headless Java
USER root
RUN yum clean all && \
    yum-config-manager --disable rhel* 1>/dev/null && \
    yum-config-manager --enable rhel-7-server-rpms && \
    yum-config-manager --enable rhel-7-server-ose-3.2-rpms && \
    export INSTALL_PKGS="nss_wrapper gettext tar git" && \
    yum clean all && \
    yum install -y --setopt=tsflags=nodocs install $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all && \

    curl -s -o /tmp/ibm-java.bin ${BASE_URL}/${JDK_PKG} && \
    chmod +x /tmp/ibm-java.bin && \
    echo "INSTALLER_UI=silent" > /tmp/response.properties && \
    echo "USER_INSTALL_DIR=/opt/ibm/java" >> /tmp/response.properties && \
    echo "LICENSE_ACCEPTED=TRUE" >> /tmp/response.properties && \
    mkdir /opt/ibm && \
    /tmp/ibm-java.bin -i silent -f /tmp/response.properties && \
    rm /tmp/response.properties && \
    rm /tmp/ibm-java.bin && \

    mkdir -p /var/lib/jenkins && \
    chown -R 1001:0 /var/lib/jenkins && \
    chmod -R g+w /var/lib/jenkins

# Copy the entrypoint
COPY configuration/* /var/lib/jenkins/
USER 1001

# Run the JNLP client by default
# To use swarm client, specify "/var/lib/jenkins/run-swarm-client" as Command
ENTRYPOINT ["/var/lib/jenkins/run-jnlp-client"]
