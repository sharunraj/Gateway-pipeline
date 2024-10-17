pipeline {
    agent any

    tools {
        maven 'my-maven'
        jdk 'JDK 21'
    }

    stages {
        stage('Clone') {
            steps {
                git url: 'https://github.com/sharunraj/Gateway-pipeline.git', branch: 'main'
            }
        }
//Test file
//         stage("Pre-Steps"){
//             steps{
//
//                 bat "docker stop springsecurity"
//                 bat "docker rm -f springsecurity"
//                 bat "docker rmi springsecurity"
//             }
//         }
        stage('Pre-Build') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        // Pre-build Docker cleanup steps
                        bat '''
                        docker stop gateway-sr || true
                        docker rm gateway-sr || true
                        docker rmi -f gateway-sr:latest || true
                        '''
                    }
                }
            }
        }
        stage('Build') {
            steps {
                bat "mvn clean install"
            }
        }
        stage('Create Docker Network') {
             steps {
                script {
                    // Create a Docker network, ignore errors if it already exists
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        bat 'docker network create my-network || echo "Network already exists"'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                bat "docker build -t gateway-sr ."
                bat "docker run --network my-network -p 8222:8222 -d --name gateway-sr gateway-sr "
            }
        }
    }//nice
}
