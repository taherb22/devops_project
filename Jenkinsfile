pipeline {
    agent any

    environment {
        SPRING_PROFILES_ACTIVE = 'dev'
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_TOKEN = credentials('sonar-token')
        DOCKERHUB_USERNAME = 'ibrahimdarghouthi1919'
        DOCKERHUB_PASSWORD = credentials('dockerhub-password')
        IMAGE_NAME = 'foyer-devops'
        VERSION = 'latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Barhoum1919/foyer_devops.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                sh """
                    mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=foyer-devops \
                    -Dsonar.projectName='Foyer DevOps' \
                    -Dsonar.host.url=$SONAR_HOST_URL \
                    -Dsonar.login=$SONAR_TOKEN
                """
            }
        }

        stage('Build Docker Image with Docker Compose') {
            steps {
                script {
                    // Build the Docker image for the 'app' service defined in docker-compose.yml
                    sh 'docker-compose build app'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub using credentials
                    sh """
                        echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
                    """
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                script {
                    // Tag the built Docker image with the proper Docker Hub name and version
                    sh 'docker tag foyer-devops:latest $DOCKERHUB_USERNAME/$IMAGE_NAME:$VERSION'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Push the tagged Docker image to Docker Hub
                    sh 'docker push $DOCKERHUB_USERNAME/$IMAGE_NAME:$VERSION'
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                sh 'mvn deploy'
            }
        }
  stage('Deploy to Kubernetes (Minikube)') {
      steps {
          sshagent(credentials: ['ssh-wsl-credentials']) {
              sh '''
                  ssh -o StrictHostKeyChecking=no ibrah@172.25.100.50 '
                      cd /mnt/c/Users/ibrah/Downloads/foyer/foyer &&
                      kubectl apply -f k8s/deployment.yaml &&
                      kubectl apply -f k8s/service.yaml
                  '
              '''
          }
      }
  }


    }

    post {
        success {
            echo 'Build, Docker push, and deployment succeeded!'
        }
        failure {
            echo 'Build, Docker push, or deployment failed.'
        }
    }
}
