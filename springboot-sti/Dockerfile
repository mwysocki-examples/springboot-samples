# springboot-sti
FROM openshift/base-centos7
MAINTAINER Maciej Wysocki <mwysocki@openwt.com>

ENV HTTP_PROXY http://10.2.64.3:3128/
ENV HTTPS_PROXY http://10.2.64.3:3128/
ENV http_proxy http://10.2.64.3:3128/
ENV https_proxy http://10.2.64.3:3128/
ENV proxy http://10.2.64.3:3128/

# Install build tools on top of base image
# Java jdk 8, Maven 3.3, Gradle 2.6
ENV GRADLE_VERSION 2.6
ENV MAVEN_VERSION 3.3.3

RUN echo "proxy=http://10.2.64.3:3128" >> /etc/yum.conf

#RUN sed 's/https/http/g' /etc/yum.repos.d/epel.repo

RUN yum install -y  \
    tar unzip bc which lsof java-1.8.0-openjdk java-1.8.0-openjdk-devel && \
    yum clean all -y
RUN curl -0 http://dev-nas.pbp-ch7.dev/apache-maven-$MAVEN_VERSION-bin.tar.gz | \
    tar -zx -C /usr/local
    
#RUN curl -x ${HTTP_PROXY} -0 http://www.eu.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | \
#    tar -zx -C /usr/local
RUN mv /usr/local/apache-maven-$MAVEN_VERSION /usr/local/maven && \
    ln -sf /usr/local/maven/bin/mvn /usr/local/bin/mvn && \
    mkdir -p /opt/openshift && \
    mkdir -p /opt/app-root/source && chmod -R a+rwX /opt/app-root/source && \
    mkdir -p /opt/s2i/destination && chmod -R a+rwX /opt/s2i/destination && \
    mkdir -p /opt/app-root/src && chmod -R a+rwX /opt/app-root/src

ENV PATH=/opt/maven/bin/:/opt/gradle/bin/:$PATH


ENV BUILDER_VERSION 1.0

LABEL io.k8s.description="Platform for building Spring Boot applications with maven or gradle" \
      io.k8s.display-name="Spring Boot builder 1.0" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,maven-3,gradle-2.6,springboot"

# TODO (optional): Copy the builder files into /opt/openshift
# COPY ./<builder_folder>/ /opt/openshift/
# COPY Additional files,configurations that we want to ship by default, like a default setting.xml

LABEL io.openshift.s2i.scripts-url=image:///usr/local/sti
COPY ./.sti/bin/ /usr/local/sti

#temporary test to check permission denied on S2I process 
#to be removed 
RUN chmod -R 777 /usr/local/sti

RUN chown -R 1001:1001 /opt/openshift

# This default user is created in the openshift/base-centos7 image
USER 1001

# Set the default port for applications built using this image
EXPOSE 8080

# Set the default CMD for the image
# CMD ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/opt/openshift/app.jar"]
CMD ["usage"]
