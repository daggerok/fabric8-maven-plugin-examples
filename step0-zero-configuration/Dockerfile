FROM daggerok/jboss-eap-7.2:7.2.0-alpine
ENTRYPOINT ${JBOSS_HOME}/bin/standalone.sh -b 0.0.0.0
EXPOSE 8080
HEALTHCHECK --interval=3s --timeout=2s --retries=9 \
        CMD wget -q --spider http://127.0.0.1:8080/ || exit 1
COPY --chown=jboss ./target/*.war ${JBOSS_HOME}/standalone/deployments/ROOT.war
