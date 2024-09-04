pipeline {
    agent any
    environment {
        SONARQUBE_URL = 'http://localhost:9000'
        SONARQUBE_TOKEN = 'squ_feb53dfea40073d2e89ee2e5c265df885bb30a41'
        DOCKERHUB_USER = 'jordanryan.jr001@gmail.com'
        DOCKERHUB_PASS = 'Docker@123'
        GITHUB_TOKEN = credentials('github-token')  // Securely reference the GitHub token
    }
    stages {
        stage('Clone Repository') {
            steps {
                script {
                    bat 'git clone https://%GITHUB_TOKEN%@github.com/JORDAN-RYAN1/CI_CD-react-node-app.git'
                }
            }
        }
        stage('Build Backend and Frontend') {
            steps {
                script {
                    bat 'docker-compose build'
                }
            }
        }
        stage('Run Unit Tests') {
            steps {
                script {
                    // Adjust this to run tests as needed in your Windows environment
                    bat 'docker-compose run backend npm test'
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
                    bat 'docker run --rm --name sonarqube-runner -v %cd%:/usr/src sonarsource/sonar-scanner-cli'
                }
            }
        }
        stage('Build Docker Images') {
            steps {
                script {
                    bat 'docker-compose build'
                }
            }
        }
        stage('Trivy Image Scan') {
            steps {
                script {
                    bat 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image myapp-backend:latest'
                    bat 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image myapp-frontend:latest'
                }
            }
        }
        stage('Push Docker Images to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        bat 'docker-compose push'
                    }
                }
            }
        }
        stage('Deploy Application') {
            steps {
                script {
                    bat 'docker-compose up -d'
                }
            }
        }
    }
    post {
        always {
            script {
                bat 'docker-compose down'
            }
        }
    }
}
