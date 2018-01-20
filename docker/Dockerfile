FROM centos:7
MAINTAINER <herzog.thomas81@gmail.com>

ARG VERSION
ARG MAVEN_REPSOTIRY_URL

LABEL name="App service" \
      summary="The app service doing anything" \
      description="The app service doing anything..." \
      url="https://github.com/OpenshiftCICD/service-app" \
      vendor="Herzog, Thomas" \
      version="1" \
      release="1" \
      run='docker run --name ${NAME} \
        -p 8080:8080 \
        -d  \
        ${IMAGE}' \
      io.k8s.description="The app service doing anything" \
      io.k8s.display-name="The app service doing anything"

ENV APP_HOME="/home/app"

USER root

RUN mkdir -p ${APP_HOME} \
    &&  useradd --no-create-home --shell /sbin/nologin -g 0 -u 1001 app

RUN yum update -y \
    && yum install -y java-1.8.0-openjdk \
    && yum clean all -y \
    && yum autoremove -y \
    && rm -rf /var/cache/yum

# Bad approach because will be in layer. Newer version of openshift allow using of buildArgs
COPY username /username
COPY password /password

RUN curl -f -u $(cat /username):$(cat /password) -L ${MAVEN_REPSOTIRY_URL}/services/services-app/${VERSION}/services-app-${VERSION}.jar \
  -o ${APP_HOME}/app.jar \
  && chmod -R g+rwX ${APP_HOME} \
  && rm -f username password


USER app

CMD exec java -jar ${APP_HOME}/app.jar
