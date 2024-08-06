// e-mail
def esubject = "Pipeline ${currentBuild.fullDisplayName} FAILED"
def ebody = """
${currentBuild.fullDisplayName} / ${currentBuild.number}
Check url: ${currentBuild.absoluteUrl}.
"""

pipeline {
    parameters {
        booleanParam(name: 'BUILD', defaultValue: true, description: 'Run Build stage')
        booleanParam(name: 'TEST', defaultValue: true, description: 'Run Test stage')
        booleanParam(name: 'PUSH', defaultValue: true, description: 'Run Push stage')
    }
    agent any
    options {
        timeout(time: 3, unit: 'HOURS')
        buildDiscarder(logRotator(numToKeepStr: '24', artifactNumToKeepStr: '24'))
        disableConcurrentBuilds()
        skipDefaultCheckout()
    }
    triggers {
        cron('H 16 3 * *')
    }
    environment {
        IMAGE="ruby"
        BOX_DATE=sh(script: 'echo "b\$(date +%g%V)"', returnStdout: true).trim()
        BOX_BUILD="${BOX_DATE}${BUILD_ID}"
    }
    stages {

        stage('Build image') {
            when {
                expression { params.BUILD == true }
            }
            parallel {
                stage('Build libvirt') {
                    agent {
                        label "libvirt && packer"
                    }
                    environment {
                        PROVIDER="libvirt"
                        PROFILE="profiles/el/images/el9-${IMAGE}.conf"
                    }
                    steps {
                        cleanWs()
                        checkout scm
                        script {
                            sh 'git submodule update --init --recursive'
                            echo "Building ${IMAGE} on the ${PROVIDER}"
                            sh "cd auto_virtual_machines/; bash -x ./cicd.sh $PROFILE 1"
                        }
                    }
                }
                stage('Build virtualbox') {
                    agent {
                        label "virtualbox && packer"
                    }
                    environment {
                        PROVIDER="virtualbox"
                        PROFILE="profiles/el/images/el9-${IMAGE}.conf"
                    }
                    steps {
                        cleanWs()
                        checkout scm
                        script {
                            sh 'git submodule update --init --recursive'
                            echo "Building ${IMAGE} on the ${PROVIDER}"
                            sh "cd auto_virtual_machines/; bash -x ./cicd.sh $PROFILE 1"
                        }
                    }
                }
                stage('Build vmware') {
                    agent {
                        label "vmware && packer"
                    }
                    environment {
                        PROVIDER="vmware"
                        PROFILE="profiles/el/images/el9-${IMAGE}.conf"
                    }
                    steps {
                        cleanWs()
                        checkout scm
                        script {
                            sh 'git submodule update --init --recursive'
                            echo "Building ${IMAGE} on the ${PROVIDER}"
                            sh "cd auto_virtual_machines/; bash -x ./cicd.sh $PROFILE 1"
                        }
                    }
                }
            }
        }

        stage('Test image') {
            when {
                expression { params.TEST == true }
            }
            parallel {
                stage('Test libvirt') {
                    agent {
                        label "libvirt && packer"
                    }
                    environment {
                        PROVIDER="libvirt"
                        PROFILE="profiles/el/images/el9-${IMAGE}.conf"
                    }
                    steps {
                        script {
                            echo "Testing ${IMAGE} on the ${PROVIDER}"
                            sh "cd auto_virtual_machines/; bash -x ./cicd.sh $PROFILE 2"
                        }
                    }
                }
                stage('Test virtualbox') {
                    agent {
                        label "virtualbox && packer"
                    }
                    environment {
                        PROVIDER="virtualbox"
                        PROFILE="profiles/el/images/el9-${IMAGE}.conf"
                    }
                    steps {
                        script {
                            echo "Testing ${IMAGE} on the ${PROVIDER}"
                            sh "cd auto_virtual_machines/; bash -x ./cicd.sh $PROFILE 2"
                        }
                    }
                }
                stage('Test vmware') {
                    agent {
                        label "vmware && packer"
                    }
                    environment {
                        PROVIDER="vmware"
                        PROFILE="profiles/el/images/el9-${IMAGE}.conf"
                    }
                    steps {
                        script {
                            echo "Testing ${IMAGE} on the ${PROVIDER}"
                            sh "cd auto_virtual_machines/; bash -x ./cicd.sh $PROFILE 2"
                        }
                    }
                }
            }
        }

        stage('Push Image') {
            when {
                expression { params.PUSH == true }
            }
            parallel {
                stage('Push libvirt') {
                    agent {
                        label "libvirt && packer"
                    }
                    environment {
                        PROVIDER="libvirt"
                        PROFILE="profiles/el/images/el9-${IMAGE}.conf"
                        GH_TOKEN=credentials('sm-release-token')
                        OWNER="SourceMation"
                        REPO="el9-${IMAGE}-vmi"
                        GH_BRANCH="main"
                    }
                    steps {
                        script {
                            echo "Pushing ${IMAGE}-${PROVIDER} to GitHub"
                            sh "cd auto_virtual_machines/; bash -x ./cicd.sh $PROFILE 3"
                        }
                        cleanWs()
                    }
                }
                stage('Push virtualbox') {
                    agent {
                        label "virtualbox && packer"
                    }
                    environment {
                        PROVIDER="virtualbox"
                        PROFILE="profiles/el/images/el9-${IMAGE}.conf"
                        GH_TOKEN=credentials('sm-release-token')
                        OWNER="SourceMation"
                        REPO="el9-${IMAGE}-vmi"
                        GH_BRANCH="main"
                    }
                    steps {
                        sleep(time: 10, unit: "SECONDS")
                        script {
                            echo "Pushing ${IMAGE}-${PROVIDER} to GitHub"
                            sh "cd auto_virtual_machines/; bash -x ./cicd.sh $PROFILE 3"
                        }
                        cleanWs()
                    }
                }
                stage('Push vmware') {
                    agent {
                        label "vmware && packer"
                    }
                    environment {
                        PROVIDER="vmware"
                        PROFILE="profiles/el/images/el9-${IMAGE}.conf"
                        GH_TOKEN=credentials('sm-release-token')
                        OWNER="SourceMation"
                        REPO="el9-${IMAGE}-vmi"
                        GH_BRANCH="main"
                    }
                    steps {
                        sleep(time: 15, unit: "SECONDS")
                        script {
                            echo "Pushing ${IMAGE}-${PROVIDER} to GitHub"
                            sh "cd auto_virtual_machines/; bash -x ./cicd.sh $PROFILE 3"
                        }
                        cleanWs()
                    }
                }
            }
        }

    }
    post {
        
        success {
            echo 'Pipeline finished successfully!'
        }
        failure {
            emailext (
                subject: esubject,
                body: ebody,
                recipientProviders: [[$class: 'DevelopersRecipientProvider']]
            )
        }
    }
}
