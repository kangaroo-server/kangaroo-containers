/**
 * Scripted pipeline for our docker containers.
 *
 * This pipeline scans all available versions from nexus central, and
 */

def versionLatest = getLatestReleaseVersion("net.krotscheck", "kangaroo-server-authz")
def versions = getAllMavenVersions("net.krotscheck", "kangaroo-server-authz")

def buildAllContainerVersions(dockerFile, suffix, versions, versionLatest) {
    def images = []
    def noSuffix = !(suffix?.trim())

    versions.each {
        def tag = it + (noSuffix ? "" : "-" + suffix)
        def container = docker.build(
                "krotscheck/kangaroo-authz:${tag}",
                " . --build-arg KANGAROO_VERSION=${it} -f ${dockerFile} --pull")
        images.push(container)
    }

    def tagLatest = (noSuffix ? "latest" : suffix)
    def latest = docker.build(
            "krotscheck/kangaroo-authz:${tagLatest}",
            " . --build-arg KANGAROO_VERSION=${versionLatest} -f ${dockerFile} --pull")
    images.push(latest)

    return images
}

/**
 * Java build pipeline for the kangaroo-container project. The initial run of
 * this is on master - actual container builds are done on the appropriate host
 * OS.
 */
pipeline {

    agent none

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    stages {

        stage('Default') {
            agent { label 'ubuntu' }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'krotscheck_docker_hub') {
                        def containers = buildAllContainerVersions('authz/Dockerfile.alpine',
                                '',
                                versions,
                                versionLatest)

                        if (env.GIT_BRANCH == 'master') {
                            containers.each { it.push() }
                        }
                    }
                }
            }
        }
        stage('Alpine') {
            agent { label 'ubuntu' }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'krotscheck_docker_hub') {
                        def containers = buildAllContainerVersions('authz/Dockerfile.alpine',
                                'alpine',
                                versions,
                                versionLatest)

                        if (env.GIT_BRANCH == 'master') {
                            containers.each { it.push() }
                        }
                    }
                }
            }
        }
        stage('Stretch') {
            agent { label 'ubuntu' }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'krotscheck_docker_hub') {
                        def containers = buildAllContainerVersions('authz/Dockerfile.stretch',
                                'stretch',
                                versions,
                                versionLatest)

                        if (env.GIT_BRANCH == 'master') {
                            containers.each { it.push() }
                        }
                    }
                }
            }
        }
        stage('Raspbian') {
            agent { label 'raspbian' }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'krotscheck_docker_hub') {
                        def containers = buildAllContainerVersions('authz/Dockerfile.raspbian',
                                'raspbian',
                                versions,
                                versionLatest)

                        if (env.GIT_BRANCH == 'master') {
                            containers.each { it.push() }
                        }
                    }
                }
            }
        }
    }
}
