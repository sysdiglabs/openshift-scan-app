pipeline {
    agent any
    environment {
        DOCKER = credentials('docker-repository-credentials')
        OPENSHIFTREF = credentials('openshift4-internal-repo')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Image') {
            steps {
                sh "sudo docker build --no-cache -f Dockerfile -t sysdigcicd/alpine:3.9.3 ."
            }
        }
        stage('Push Image (Staging repo)') {
            steps {
                sh "sudo docker login --username ${DOCKER_USR} --password ${DOCKER_PSW}"
                sh "sudo docker push sysdigcicd/alpine:3.9.3"
                sh "echo docker.io/sysdigcicd/alpine:3.9.3 > sysdig_secure_images"
            }
        }
        stage('Scanning Image') {
            steps {
                sysdigSecure 'sysdig_secure_images'
            }
        }
        stage('Push Image (OpenShift internal repo)') {
            steps {
                sh "sudo docker login https://registry.apps.openshift4.openshift-sysdig.net --username ${OPENSHIFTREF_USR} --password ${OPENSHIFTREF_PSW}"
                sh "sudo docker tag sysdigcicd/alpine:3.9.3 registry.apps.openshift4.openshift-sysdig.net/sysdig-image/alpine:3.9.3"
                sh "sudo docker push registry.apps.openshift4.openshift-sysdig.net/sysdig-image/alpine:3.9.3"
            }
        }
   }
}
