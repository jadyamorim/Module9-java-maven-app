#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/jadyamorim/Module9-jenkins-shared-library.git',
    credentialsID: 'github-credentials'
    ]
)

pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        IMAGE_NAME = 'jadyamorim/jadydevops:java-maven-2.0'
    }
    stages {
        stage('build app') {
            steps {
                echo 'building application jar...'
                buildJar()
            }
        }
        stage('build image') {
            steps {
                script {
                    echo 'building the docker image...'
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    echo 'deploying docker image to EC2...'

                    def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}""

                    sshagent(['ec2-server-key']) {
                        sh "scp server-cmds.sh ec2-user@16.174.61.116:/home/ec2-user"
                        sh "scp docker-compose.yaml ec2-user@16.174.61.116:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@16.174.61.116 ${shellCmd}"
                    }
                }
            }
        }
    }
}
