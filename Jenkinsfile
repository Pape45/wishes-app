pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'resolution-tracker'
        DOCKER_TAG = 'latest'
        GITHUB_REPO = 'https://github.com/your-org/resolution-tracker.git'
    }
    
    parameters {
        choice(
            name: 'DEPLOYMENT_ENV',
            choices: ['development', 'staging', 'production'],
            description: 'Select deployment environment'
        )
        string(
            name: 'VERSION',
            defaultValue: '1.0.0',
            description: 'Application version to deploy'
        )
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: env.GITHUB_REPO
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Run Tests') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        sh 'npm run test:unit'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        sh 'npm run test:integration'
                    }
                }
            }
        }
        
        stage('Code Quality') {
            steps {
                sh 'npm run lint'
                sh 'npm run sonar-scanner'
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm run build'
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        
        stage('Deploy') {
            when {
                expression { params.DEPLOYMENT_ENV != 'production' }
            }
            steps {
                script {
                    def deployScript = "deploy-${params.DEPLOYMENT_ENV}.sh"
                    sh "chmod +x scripts/${deployScript}"
                    sh "scripts/${deployScript} ${params.VERSION}"
                }
            }
        }
        
        stage('Production Deploy') {
            when {
                expression { params.DEPLOYMENT_ENV == 'production' }
            }
            input {
                message 'Deploy to production?'
                ok 'Proceed'
            }
            steps {
                sh "scripts/deploy-production.sh ${params.VERSION}"
            }
        }
        
        stage('Health Check') {
            steps {
                sh 'scripts/health-check.sh'
            }
        }
    }
    
    post {
        always {
            junit '**/test-results/*.xml'
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'coverage',
                reportFiles: 'index.html',
                reportName: 'Coverage Report'
            ])
        }
        success {
            slackSend(
                color: 'good',
                message: "Build succeeded: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
            )
        }
        failure {
            slackSend(
                color: 'danger',
                message: "Build failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
            )
        }
    }
}