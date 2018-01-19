FROM centos:7
MAINTAINER <herzog.thomas81@gmail.com>

ARG VERSION

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

# Bad approach because will be in layer. Newer version of openshift allow using of buildArgs
COPY username /username
COPY password /password

RUN env

RUN mkdir -p ${APP_HOME} \
    &&  useradd --no-create-home --shell /sbin/nologin -g 0 -u 1001 app

RUN yum update -y \
    && yum install -y java-1.8.0-openjdk \
    && chmod -R g+rwX ${APP_HOME} \
    && yum clean all -y \
    && yum autoremove -y \
    && rm -f username password \
    && rm -rf /var/cache/yum

RUN curl -u $(cat /username):$(cat /password) -L ${MAVEN_REPSOTIRY_URL}/services/${VERSION}/service-app-${VERSION}.jar \
  -o ${APP_HOME}/app.jar

USER app

CMD exec java -jar app.jar
