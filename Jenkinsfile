// This file is part of hoertech-openmha-keyring
// Copyright © 2020 HörTech gGmbH
// Copyright © 2022 Hörzentrum Oldenburg gGmbH

pipeline {
    agent {label "mhadev && linux && x86_64"}
    stages {
        stage("build") {
            steps {
                sh 'mhamakedeb hoertech-openmha-keyring.csv $(cat version) all'
                stash name: "deb", includes: '*.deb'
            }
        }
        stage("store debian packages for apt") {
            agent {
                docker {
                    image "hoertech/docker-buildenv:mha_x86_64-linux-gcc-13"
                    label "docker_x86_64"
                    alwaysPull true
                    args "-v /home/u:/home/u --hostname docker"
                }
            }
            steps {
                // receive all deb packages from openmha build
                unstash "deb"
                archiveArtifacts "*.deb"

                // Publishing the deb for these systems:
                sh "mkdir -p deb/bionic"
                sh "ln *.deb deb/bionic"
                sh "mkdir -p deb/focal"
                sh "ln *.deb deb/focal"
                sh "mkdir -p deb/jammy"
                sh "ln *.deb deb/jammy"
                sh "mkdir -p deb/bullseye"
                sh "ln *.deb deb/bullseye"

                // Copies the new debs to the stash of existing debs,
                sh "BRANCH_NAME=master make storage"

                build job:         "/Packaging/hoertech-aptly/master",
                      quietPeriod: 300,
                      wait:        false
            }
        }
    }

    // Email notification on failed build taken from
    // https://jenkins.io/doc/pipeline/tour/post/
    // multiple recipients are comma-separated:
    // https://jenkins.io/doc/pipeline/steps/workflow-basic-steps/#-mail-%20mail
    post {
        failure {
            mail to: 'herzke@hz-ol.de',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
