# Dremio 2.0.1 for Tideworks Insight

FROM openjdk:8-jdk

ENV DREMIO_HOME=/opt/dremio \
	DREMIO_PID_DIR=/var/run/dremio \
        DREMIO_STOP_TIMEOUT=120 \
        DIST='' \
        COORDINATOR_ENABLED=true \
        COORDINATOR_MASTER_ENABLED=true \
        EXECUTOR_ENABLED=true \
        ZOOKEEPER='' \
        DREMIO_MAX_HEAP_MEMORY_SIZE_MB=8192 \
        DREMIO_MAX_DIRECT_MEMORY_SIZE_MB=16384 \
	MASTER_IP='' \
	MASTER_HOSTNAME=''

ENV DREMIO_CONF_DIR=${DREMIO_HOME}/conf \
	DREMIO_LOG_DIR=${DREMIO_HOME}/log \
	LOCAL=${DREMIO_HOME}/data

RUN apt-get update && apt-get install -y \
	htop  \
	nano \
	gosu \
# Add Users/Groups
        && groupadd -r dremio \
        && useradd -r -g dremio -d /var/lib/dremio -s /sbin/nologin dremio \
# Create Directories
	&& mkdir -p ${DREMIO_HOME} \
	&& mkdir -p ${DREMIO_LOG_DIR} \
	&& mkdir -p ${DREMIO_CONF_DIR} \
	&& mkdir -p ${DREMIO_PID_DIR} \
	&& mkdir -p ${LOCAL} \
# Extract Application(s)
	&& cd /tmp \
	&& wget https://download.dremio.com/community-server/2.0.1-201804132205050000-10b1de0/dremio-community-2.0.1-201804132205050000-10b1de0.tar.gz \
	&& tar -xvf dremio-community-2.0.1-201804132205050000-10b1de0.tar.gz -C ${DREMIO_HOME}/ --strip-components=1 \
	&& cp ${DREMIO_CONF_DIR}/dremio.conf ${DREMIO_CONF_DIR}/dremio.conf.bak \
# docker-entrypoint.sh
        && echo "#!/bin/bash" > /usr/local/bin/docker-entrypoint.sh \
        && echo "" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"paths: \" > \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"{ \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "echo \"  # the local path for dremio to store data. \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "echo \"  local: \${LOCAL} \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"  # the distributed path Dremio data including job results, downloads, uploads, etc \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "[[ -z \${DIST} ]] && echo \"  #dist: \\\"pdfs://\\\"\\\${paths.local}\\\"/pdfs\\\" \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "[[ ! -z \${DIST} ]] && echo \"  dist: \${DIST} \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"} \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"services: \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"{ \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"  coordinator.enabled: \${COORDINATOR_ENABLED}, \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"  coordinator.master.enabled: \${COORDINATOR_MASTER_ENABLED}, \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"  executor.enabled: \${EXECUTOR_ENABLED} \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"} \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "echo \"\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "[[ ! -z \${ZOOKEEPER} ]] && echo \"zookeeper: \\\"\${ZOOKEEPER}\\\"\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "[[ ! -z \${MASTER_IP} && ! -z \${MASTER_HOSTNAME} ]] && cp /etc/hosts ~/hosts.new && sed -i '/'\"\${MASTER_IP}\"'/d' ~/hosts.new && cp -f ~/hosts.new /etc/hosts && echo \"\${MASTER_IP}      \${MASTER_HOSTNAME}\" >> /etc/hosts" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "chown -h dremio:dremio \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "function gracefulShutdown" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "{" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "  echo Shutting down!" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "  timeout ${DREMIO_STOP_TIMEOUT} ${DREMIO_HOME}/bin/dremio stop" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  kill -9 \`ps -aef | grep 'com.dremio.dac.daemon.DremioDaemon dremio start' | grep -v grep | awk '{print \$2}'\`" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "}" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "function isAlive" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "{" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  timeout 10 \${DREMIO_HOME}/bin/dremio status > /dev/null 2>&1" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  status=\$?" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  if [ \$status -ne 0 ]; then" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "    echo \"Dremio has exited.\"" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "    gracefulShutdown" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "    exit -1" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  fi" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "}" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "trap gracefulShutdown SIGTERM" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "trap gracefulShutdown SIGINT" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "" >> /usr/local/bin/docker-entrypoint.sh \
	&& echo "echo \"Starting Dremio... \`date\`\"" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "gosu dremio ${DREMIO_HOME}/bin/dremio start" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "while sleep 20; do" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  isAlive" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  ps aux | grep 'com.dremio.dac.daemon.DremioDaemon dremio start' | grep -q -v grep" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  PROCESS_1_STATUS=\$?" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  if [ \$PROCESS_1_STATUS -ne 0 ]; then" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "    echo \"Dremio process has exited.\"" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "    gracefulShutdown" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "    exit -1" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "  fi" >> /usr/local/bin/docker-entrypoint.sh \
        && echo "done" >> /usr/local/bin/docker-entrypoint.sh \
        && chmod +x /usr/local/bin/docker-entrypoint.sh \
        && ln -s /usr/local/bin/docker-entrypoint.sh / \
# Set File/Directory Ownership
        && chown -R dremio:dremio ${DREMIO_HOME} \
        && chown -R dremio:dremio ${DREMIO_LOG_DIR} \
        && chown -R dremio:dremio ${DREMIO_CONF_DIR} \
        && chown -R dremio:dremio ${DREMIO_PID_DIR} \
        && chown -R dremio:dremio ${LOCAL} \
# Cleanup
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


VOLUME ${DREMIO_HOME}

EXPOSE 9047 2181

CMD [ "docker-entrypoint.sh" ]
