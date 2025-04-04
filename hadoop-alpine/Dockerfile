FROM alpine:3.16 as builder

# Instala dependências
RUN apk add --no-cache \
    openjdk8-jre \
    bash \
    curl \
    gnupg \
    procps \
    openssl \
    python3 \
    libc6-compat

ENV HADOOP_VERSION=3.3.6
ENV HADOOP_HOME=/opt/hadoop
ENV PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

# Download e instalação do Hadoop
RUN mkdir -p ${HADOOP_HOME} && \
    cd /tmp && \
    wget -q https://archive.apache.org/dist/hadoop/core/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz && \
    tar xzf hadoop-${HADOOP_VERSION}.tar.gz -C ${HADOOP_HOME} --strip-components=1 && \
    rm hadoop-${HADOOP_VERSION}.tar.gz

# Cria diretório de configuração e copia arquivos
RUN mkdir -p ${HADOOP_HOME}/etc/hadoop
COPY config/* ${HADOOP_HOME}/etc/hadoop/
RUN chmod -R 644 ${HADOOP_HOME}/etc/hadoop/*.xml

# Configura usuário e permissões
RUN addgroup -S hadoop && adduser -S -G hadoop hadoop && \
    chown -R hadoop:hadoop ${HADOOP_HOME}

RUN mkdir -p /hadoop-data/namenode /hadoop-data/datanode && \
    chown -R hadoop:hadoop /hadoop-data

WORKDIR ${HADOOP_HOME}
USER hadoop

# Imagem final
FROM alpine:3.16

RUN addgroup -g 1000 hadoop && \
    adduser -u 1000 -G hadoop -h /home/hadoop -D hadoop && \
    mkdir -p /opt/hadoop/logs && \
    chown -R hadoop:hadoop /opt/hadoop

COPY --from=builder /opt/hadoop /opt/hadoop
COPY --from=builder /hadoop-data /hadoop-data
COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /etc/group /etc/group

RUN apk add --no-cache \
    openjdk8-jre \
    bash \
    libc6-compat \
    procps \
    python3 && \
    ln -s /lib/libc.musl-x86_64.so.1 /lib/ld-linux-x86-64.so.2

ENV HADOOP_HOME=/opt/hadoop
ENV PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
ENV HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
ENV HADOOP_LOG_DIR=$HADOOP_HOME/logs
ENV JAVA_HOME=/usr/lib/jvm/java-1.8-openjdk

# Cria diretório de logs e ajusta permissões
RUN mkdir -p ${HADOOP_HOME}/logs && \
    chown hadoop:hadoop ${HADOOP_HOME}/logs

WORKDIR ${HADOOP_HOME}
USER hadoop

EXPOSE 8020 9000 9870 9864 9866 9867

CMD ["bash"]