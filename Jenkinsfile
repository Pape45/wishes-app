// Jenkinsfile
pipeline {
    agent any
    
    parameters {
        choice(name: 'MONTH', choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'], description: 'Select month for wish tracking')
        string(name: 'USER_NAME', defaultValue: '', description: 'Enter user name for tracking')
    }
    
    stages {
        stage('Build') {
            steps {
                echo "Building pipeline for ${params.MONTH} wishes"
                sh '''
                    mkdir -p build
                    echo "Build configuration for ${MONTH}" > build/config.txt
                '''
            }
        }
        
        stage('Test') {
            steps {
                echo "Testing ${params.MONTH} pipeline configuration"
                sh '''
                    # Validate configuration
                    if [ -f build/config.txt ]; then
                        echo "Configuration validation passed"
                    else
                        exit 1
                    fi
                '''
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Deploying ${params.MONTH} tracking system"
                sh '''
                    mkdir -p deploy
                    cp build/config.txt deploy/
                    echo "Deployment timestamp: $(date)" >> deploy/metadata.txt
                '''
            }
        }
        
        stage('Validation') {
            steps {
                echo "Validating deployment for ${params.MONTH}"
                sh '''
                    if [ -f deploy/config.txt ] && [ -f deploy/metadata.txt ]; then
                        echo "Deployment validation passed"
                    else
                        exit 1
                    fi
                '''
            }
        }
    }
    
    post {
        success {
            echo "Pipeline for ${params.MONTH} completed successfully"
        }
        failure {
            echo "Pipeline for ${params.MONTH} failed"
        }
    }
}