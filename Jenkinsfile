pipeline {
    agent any
    environment {
        registry = 'saadmakhdoom/coin-change'
        dockerImage = ''
        DOCKERHUB_CREDENTIALS = credentials('saadmakhdoom-dockerhub')
    }
    stages {
        stage('Clone the repo') {
            steps {
                echo 'Clone the repo'
                sh 'rm -fr k8s_classtask'
                echo 'Get the repo'
                sh 'git clone https://github.com/hammadmajeed/k8s_classtask.git'
                echo 'Set the dir'
                sh 'cd k8s_classtask'
                sh 'pwd'
            }
        }
        stage('Install Dependencies') {
            steps {
                echo 'Install dependencies'
                sh 'pwd'
                sh 'ls'
                dir('k8s_classtask') {
                    sh 'pip3 install -r requirements.txt'
                }
            }
        }
        
        stage('Build image, Push to Hub, Run the image') {
            steps {
                echo 'Docker image build '
                sh 'pwd'
                sh 'ls'
                script{
                    dir('k8s_classtask') {
                        dockerImage = docker.build("saadmakhdoom/coin-change:latest")
                    }
                    if(dockerImage){
                        echo 'Docker Image built'
                        withDockerRegistry([credentialsId: "saadmakhdoom-dockerhub", url: ""]) {
                            dockerImage.push()
                        }
                        sh 'docker run -d -p 8090:8080 saadmakhdoom/coin-change:latest'
                    }else{
                        error "Docker image build failed."
                    }
                }
            }
        }
        
       stage('Deploy the image from Docker Hub to kubernetes') {
            steps {
                
                  script {
                    bat "minikube start --force-systemd"
                  
                }
            }
       }
        
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
