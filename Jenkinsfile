@Library('jenkins-helpers@v0.1.23') _

def label = "bazel-maven-proxy-${UUID.randomUUID().toString()}"
podTemplate(
        label: label,
        annotations: [
                podAnnotation(key: "jenkins/build-url", value: env.BUILD_URL ?: ""),
                podAnnotation(key: "jenkins/github-pr-url", value: env.CHANGE_URL ?: ""),
        ],
        containers: [
            containerTemplate(name: 'bazel',
                              image: 'gcr.io/cloud-builders/bazel',
                              resourceRequestCpu: '500m',
                              resourceLimitCpu: '4000m',
                              resourceRequestMemory: '4000Mi',
                              resourceLimitMemory: '4000Mi',
                              command: '/bin/cat -',
                              ttyEnabled: true),
        ],
        volumes: [
            secretVolume(secretName: 'jenkins-docker-builder', mountPath: '/jenkins-docker-builder'),
            hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock'),
        ]) 
{
    node(label) {
        container('jnlp') {
            stage("checking out code") {
                checkout(scm)
            }
        }
        container('bazel') {
            stage("bazel build") {
                sh(returnStatus: true, script: 'docker login -u _json_key -p "$(cat /jenkins-docker-builder/credentials.json)" https://eu.gcr.io')
                sh(returnStatus: true, script: 'bazel run //:container_push')
            }
        }
    }
}
