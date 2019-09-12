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
                              resourceLimitMemory: '4000Mi'),
        ],
        volumes: [
            secretVolume(secretName: 'maven-credentials', mountPath: '/maven-credentials'),
            secretVolume(secretName: 'jenkins-docker-builder', mountPath: '/jenkins-docker-builder'),
        ]) 
{
    node(label) {
        container('bazel') {
            stage("checking out code") {
                dir('bazel-maven-proxy') {
                    checkout(scm)
                }
            }
            stage("bazel build") {
                dir('bazel-maven-proxy') {
                    sh(returnStatus: true, script: 'bazel run //:container_push')
                }
            }
        }
    }
}
