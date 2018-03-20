/**
 * Scripted pipeline for our docker containers.
 *
 * This pipeline scans all available versions from nexus central, and
 */

def versionLatest = getLatestReleaseVersion("net.krotscheck", "kangaroo-server-authz")

/**
 * Jenkins build pipeline for the kangaroo-container project. The initial run of
 * this is on master - actual container builds are done on the appropriate host OS.
 */
pipeline {

    agent none

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    stages {

        stage('Default') {
            agent { label 'worker' }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'krotscheck_docker_hub') {
                        def args = " . --build-arg KANGAROO_VERSION=${versionLatest} -f authz/Dockerfile.alpine --pull"

                        docker.build("krotscheck/kangaroo-authz:latest", args)
                        docker.build("krotscheck/kangaroo-authz:${versionLatest}", args)
                        docker.build("krotscheck/kangaroo-authz:latest-alpine", args)
                        docker.build("krotscheck/kangaroo-authz:${versionLatest}-alpine", args)
                    }
                }
            }
        }

        stage('Stretch') {
            agent { label 'worker' }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'krotscheck_docker_hub') {
                        def args = " . --build-arg KANGAROO_VERSION=${versionLatest} -f authz/Dockerfile.stretch --pull"

                        docker.build("krotscheck/kangaroo-authz:latest-stretch", args)
                        docker.build("krotscheck/kangaroo-authz:${versionLatest}-stretch", args)
                    }
                }
            }
        }

        stage('Raspbian') {
            agent { label 'raspbian' }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'krotscheck_docker_hub') {
                        def args = " . --build-arg KANGAROO_VERSION=${versionLatest} -f authz/Dockerfile.raspbian --pull"

                        docker.build("krotscheck/kangaroo-authz:latest-raspbian", args)
                        docker.build("krotscheck/kangaroo-authz:${versionLatest}-raspbian", args)
                    }
                }
            }
        }
    }

    post {

        /**
         * When the build status changed, send the result.
         */
        changed {
            script {
                notifySlack(currentBuild.currentResult)
            }
        }
    }
}
