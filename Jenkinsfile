pipeline {
    agent any
    tools {
        go 'go1.20'
    }
    environment {
        GO120MODULE = 'on'
        CGO_ENABLED = 0 
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    }
    stages {
        stage('Dokcer Stop and Delete Container') {
            sh 'docker stop api-gin'
            sh 'docker rm api-gin'
        }
        stage('Build') { 
            steps {
                sh 'go version'
                sh 'go get ./...'
                sh 'docker rmi gemm123/api-gin'
                sh 'docker build . -t gemm123/api-gin'
            }
        }
        stage('Docker Push') {
            agent any
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerhubPassword', usernameVariable: 'dockerhubUser')]) {
                sh "docker login -u ${env.dockerhubUser} -p ${env.dockerhubPassword}"
                sh 'docker push gemm123/api-gin'
                }
            }
        }
        stage('Dokcer Create Container') {
            sh 'docker container create --name api-gin -p 8081:8080 gemm123/api-gin'
            sh 'docker container start api-gin'
        }
    }
}