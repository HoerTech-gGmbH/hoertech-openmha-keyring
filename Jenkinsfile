// This file is part of hoertech-openmha-keyring
// Copyright © 2020 HörTech gGmbH

pipeline {
    agent {label "mhadev && linux && x86_64"}
    stages {
        stage("build") {
            steps {
                sh 'mhamakedeb hoertech-openmha-keyring $(cat version) all'
                stash name: "deb", includes: '*.deb'
            }
        }
        stage("store debian packages for apt") {
            agent {label "aptly"}
            steps {
                // receive all deb packages from openmha build
                unstash "deb"
                archiveArtifacts "*.deb"

                // Publishing the deb for these systems:
                sh "mkdir -p deb/bionic"
                sh "ln *.deb deb/bionic"
                sh "mkdir -p deb/buster"
                sh "ln *.deb deb/buster"
                sh "mkdir -p deb/xenial"
                sh "ln *.deb deb/xenial"
                sh "mkdir -p deb/focal"
                sh "ln *.deb deb/focal"
                
                // Copies the new debs to the stash of existing debs,
                sh "make storage"

                build job:         "/hoertech-aptly/master",
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
            mail to: 't.herzke@hoertech.de',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
