pipeline {
    agent {
        node {
            label 'dockerhost-build-server'
        }
    }

    tools {
        maven 'maven-3.9.6'
    }

    stages {

        stage('Cleanup old container') {
            steps {
                echo 'Removing old container if exists...'
                sh '''
                docker rm -f devops-web-project-server || true
                '''
            }
        }

        stage('Packaging') {
            steps {
                echo 'Packaging...'
                sh 'mvn clean package'
            }
        }

        stage('Copying war file') {
            steps {
                echo 'Copying war file...'
                sh 'mv target/*.war .'
            }
        }

        stage('Cleanup docker images') {
            steps {
                echo 'Cleaning old docker images...'
                sh 'docker system prune -a --force --filter "label=devops-web-project-server"'
            }
        }

        stage('Build image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t JAGO-DEV-2026/devops-web-project:v1 --label devops-web-project-server .'
            }
        }

        stage('Run container') {
            steps {
                echo 'Running container...'
                sh '''
                docker run -d \
                  --name devops-web-project-server \
                  --label devops-web-project-server \
                  -p 8081:8080 \
                  JAGO-DEV-2026/devops-web-project:v1
                '''
            }
        }
    }
}
