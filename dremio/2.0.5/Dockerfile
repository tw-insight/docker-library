# Dremio 2.0.5 for Tideworks Insight

FROM openjdk:8-jdk

ENV DREMIO_HOME=/opt/dremio \
    DREMIO_PID_DIR=/var/run/dremio \
    DREMIO_STOP_TIMEOUT=120 \
    DREMIO_START_TIMEOUT=600 \
    DIST='' \
    ACCELERATOR='' \
    DOWNLOADS='' \
    UPLOADS='' \
    RESULTS='' \
    COORDINATOR_ENABLED=true \
    COORDINATOR_MASTER_ENABLED=true \
    EXECUTOR_ENABLED=true \
    ZOOKEEPER='' \
    DREMIO_MAX_HEAP_MEMORY_SIZE_MB=16384 \
    DREMIO_MAX_DIRECT_MEMORY_SIZE_MB=16384 

ENV DREMIO_CONF_DIR=${DREMIO_HOME}/conf \
    DREMIO_LOG_DIR=${DREMIO_HOME}/log \
    LOCAL=${DREMIO_HOME}/data

RUN apt-get update && apt-get install -y \
        htop  \
        nano \
        gosu \
# Add Users/Groups
    && groupadd -r --gid 50006 dremio \
    && useradd -r --uid 50006 --gid 50006 -d /var/lib/dremio -s /sbin/nologin dremio \
# Create Directories
    && mkdir -p ${DREMIO_HOME} \
    && mkdir -p ${DREMIO_LOG_DIR} \
    && mkdir -p ${DREMIO_CONF_DIR} \
    && mkdir -p ${DREMIO_PID_DIR} \
    && mkdir -p ${LOCAL} \
# Extract Application(s)
    && cd /tmp \
    && wget --no-check-certificate https://download.dremio.com/community-server/2.0.5-201806021755080191-767cfb5/dremio-community-2.0.5-201806021755080191-767cfb5.tar.gz \
    && tar -xvf dremio-community-2.0.5-201806021755080191-767cfb5.tar.gz -C ${DREMIO_HOME}/ --strip-components=1 \
    && cp ${DREMIO_CONF_DIR}/dremio.conf ${DREMIO_CONF_DIR}/dremio.conf.bak \
# docker-entrypoint.sh
    && echo "#!/bin/bash" > /usr/local/bin/docker-entrypoint.sh \
    && echo "" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "echo \"paths: \" > \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "echo \"{ \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "echo \"  # the local path for dremio to store data. \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "echo \"  local: \${LOCAL}\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "echo \"\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "echo \"  # the distributed path Dremio data including job results, downloads, uploads, etc \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "if [ -z \${DIST} ]; then" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  #dist: \\\"pdfs://\\\"\\\${paths.local}\\\"/pdfs\\\" \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "else" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  dist: \${DIST}\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "fi" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "if [ ! -z \${ACCELERATOR} ]; then" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  # storage area for the accelerator cache \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  accelerator: \${ACCELERATOR}/accelerator\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "fi" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "if [ ! -z \${DOWNLOADS} ]; then" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  # staging area for json and csv ui downloads \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  downloads: \${DOWNLOADS}/downloads\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "fi" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "if [ ! -z \${UPLOADS} ]; then" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  # stores uploaded data associated with user home directories \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  uploads: \${UPLOADS}/uploads \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "fi" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "if [ ! -z \${RESULTS} ]; then" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"\" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  # stores data associated with the job results cache \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo \"  results: \${RESULTS}/results \" >> \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "fi" >> /usr/local/bin/docker-entrypoint.sh \
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
    && echo "chown -h dremio:dremio \${DREMIO_CONF_DIR}/dremio.conf" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "function gracefulShutdown" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "{" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  echo Shutting down!" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  timeout \${DREMIO_STOP_TIMEOUT} ${DREMIO_HOME}/bin/dremio stop" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  kill -9 \`ps -aef | grep 'com.dremio.dac.daemon.DremioDaemon dremio start' | grep -v grep | awk '{print \$2}'\`" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "}" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "function isAlive" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "{" >> /usr/local/bin/docker-entrypoint.sh \
    && echo "  timeout \${DREMIO_START_TIMEOUT} \${DREMIO_HOME}/bin/dremio status > /dev/null 2>&1" >> /usr/local/bin/docker-entrypoint.sh \
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

EXPOSE 9047 2181 31010 45678

CMD [ "docker-entrypoint.sh" ]
