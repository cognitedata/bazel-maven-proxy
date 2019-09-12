@Library('jenkins-helpers@v0.1.23') _

def label = "bazel-maven-proxy-${UUID.randomUUID().toString()}"
podTemplate(
        label: label,
        annotations: [
                podAnnotation(key: "jenkins/build-url", value: env.BUILD_URL ?: ""),
                podAnnotation(key: "jenkins/github-pr-url", value: env.CHANGE_URL ?: ""),
        ],
        containers: [
            containerTemplate(name: 'jnlp',
                              image: 'eu.gcr.io/cognitedata/build-client-java:53b4aca',
                              resourceRequestCpu: '100m',
                              resourceLimitCpu: '4000m',
                              resourceRequestMemory: '4000Mi',
                              resourceLimitMemory: '4000Mi',
                              envVars: [
                                envVar(key: 'MAVEN_OPTS', value: '-Xms256m -Xmx3000m -Dmaven.artifact.threads=30 -Dorg.slf4j.simpleLogger.dateTimeFormat=HH:mm:ss,SSS -Dorg.slf4j.simpleLogger.showDateTime=true')
                              ],
                              args: '${computer.jnlpmac} ${computer.name}'),
            containerTemplate(name: 'bazel',
                              image: 'gcr.io/cloud-builders/bazel',
                              resourceRequestCpu: '500m',
                              resourceLimitCpu: '4000m',
                              resourceRequestMemory: '4000Mi',
                              resourceLimitMemory: '4000Mi'),
        ],
        volumes: [
            secretVolume(secretName: 'jenkins-docker-builder', mountPath: '/jenkins-docker-builder'),
            hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock'),
        ]) 
{
    node(label) {
        container('jnlp') {
            stage("checking out code") {
                dir('bazel-maven-proxy') {
                    checkout(scm)
                }
            }
        }
        container('bazel') {
            stage("bazel build") {
                dir('bazel-maven-proxy') {
                    sh(returnStatus: true, script: 'bazel run //:container_push')
                }
            }
        }
    }
}
