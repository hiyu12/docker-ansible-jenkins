currentBuild.displayName = "myapp #"+currentBuild.number
pipeline {
    agent any
    tools {
      maven 'maven3'
    }
    environment {
        DOCKER_TAG = getdockertag()
    }
    stages {
        stage("SCM Checkout") {
            steps {
                git branch: 'main', credentialsId: 'git-login', url: 'https://github.com/hiyu12/docker-ansible-jenkins.git'
            }    
        }
        stage("maven build"){
            steps {
                sh "mvn clean package"
            }
        }
        stage("Build Docker Image"){
            steps {
                sh "docker build . -t akash5791/myapp:${DOCKER_TAG}"
            }
        }
        stage("Push Docker Image"){
            steps {
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerpass')]) {
                  sh "docker login -u akash5791 -p ${dockerpass}" 
                }
                sh "docker push akash5791/myapp:${DOCKER_TAG}"
            }
        }
        stage("Deploy on Container") {
            steps {
                ansiblePlaybook credentialsId: 'private-key', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible-ak', inventory: 'inv', playbook: 'playbook.yaml'
            }
        }
    }
}
def getdockertag(){
    def tag = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return tag
}
