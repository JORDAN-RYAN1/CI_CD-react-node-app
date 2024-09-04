pipeline {
    agent any
    environment {
        SONARQUBE_URL = 'http://localhost:9000'
        SONARQUBE_TOKEN = 'squ_feb53dfea40073d2e89ee2e5c265df885bb30a41'
        DOCKERHUB_USER = 'jordanryan.jr001@gmail.com'
        DOCKERHUB_PASS = 'Docker@123'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/JORDAN-RYAN1/CI_CD-react-node-app.git'
            }
        }
        stage('Build Backend and Frontend') {
            steps {
                script {
                    sh 'docker-compose build'
                }
            }
        }
        stage('Run Unit Tests') {
            steps {
                script {
                    // Here you can run unit tests for both frontend and backend if you have them.
                    // For example, for Node.js backend:
                    sh 'docker-compose run backend npm test'
                }
            }
        }
        stage('Dependency-Check (OWASP)') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'OWASP Dependency-Check'
            }
        }
        stage('Snyk Security Scan') {
            steps {
                snykSecurity snykInstallation: 'snyk', organisation: 'your-org', projectName: 'docker-react-node-app'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'docker run --rm --name sonarqube-runner -v $(pwd):/usr/src sonarsource/sonar-scanner-cli'
                }
            }
        }
        stage('Build Docker Images') {
            steps {
                script {
                    sh 'docker-compose build'
                }
            }
        }
        stage('Trivy Image Scan') {
            steps {
                script {
                    sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image myapp-backend:latest'
                    sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image myapp-frontend:latest'
                }
            }
        }
        stage('Push Docker Images to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        sh 'docker-compose push'
                    }
                }
            }
        }
        stage('Deploy Application') {
            steps {
                script {
                    sh 'docker-compose up -d'
                }
            }
        }
    }
    post {
        always {
            // Clean up any stopped containers and images
            sh 'docker-compose down'
        }
    }
}
