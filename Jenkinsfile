pipeline {
    agent any
    
    parameters {
        choice(name: 'MONTH', choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'], description: 'Select month for wish tracking')
        string(name: 'DAY', defaultValue: '1', description: 'Enter current day (1-31)')
        booleanParam(name: 'OBJECTIVE_COMPLETED', defaultValue: false, description: 'Mark if objective is completed')
    }
    
    environment {
        ALERT_MESSAGE = ''
        CONFIG_FILE = 'src/pipelines/objectives-config.yaml'
    }
    
    stages {
        stage('Load Configuration') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    
                    // Store configuration in environment variables
                    env.MONTH_NAME = monthConfig.name
                    env.MONTH_DESCRIPTION = monthConfig.description
                    env.SUCCESS_CRITERIA = monthConfig.success_criteria.join('\n- ')
                    
                    // Set reminder message based on day
                    def day = params.DAY.toInteger()
                    if (day <= 15) {
                        env.ALERT_MESSAGE = monthConfig.reminder_before_15
                    } else {
                        env.ALERT_MESSAGE = monthConfig.reminder_after_15
                    }
                }
            }
        }
        
        stage('Validate Objectives') {
            steps {
                script {
                    echo """
                    Monthly Objective Details:
                    -------------------------
                    Month: ${params.MONTH}
                    Goal: ${env.MONTH_NAME}
                    Description: ${env.MONTH_DESCRIPTION}
                    Day: ${params.DAY}
                    
                    Success Criteria:
                    - ${env.SUCCESS_CRITERIA}
                    """
                }
            }
        }
        
        stage('Check Progress') {
            steps {
                script {
                    if (!params.OBJECTIVE_COMPLETED) {
                        echo "WARNING: ${env.ALERT_MESSAGE}"
                        
                        def config = readYaml file: CONFIG_FILE
                        def urgencyLevel = (params.DAY.toInteger() > 25) ? 'urgent' : 'normal'
                        def messageTemplate = params.DAY.toInteger() <= 15 ? 
                            config.notification_settings.urgency_levels[urgencyLevel].before_15 :
                            config.notification_settings.urgency_levels[urgencyLevel].after_15
                            
                        def daysLeft = 30 - params.DAY.toInteger()
                        echo messageTemplate
                            .replace('{month}', params.MONTH)
                            .replace('{days_left}', "${daysLeft}")
                    } else {
                        def config = readYaml file: CONFIG_FILE
                        echo config.notification_settings.urgency_levels.success
                            .replace('{month}', params.MONTH)
                    }
                }
            }
        }
        
        stage('Generate Report') {
            steps {
                sh """
                    mkdir -p reports
                    echo "Status Report for ${params.MONTH} - Day ${params.DAY}" > reports/status.txt
                    echo "----------------------------------------" >> reports/status.txt
                    echo "Goal: ${env.MONTH_NAME}" >> reports/status.txt
                    echo "Description: ${env.MONTH_DESCRIPTION}" >> reports/status.txt
                    echo "Objectives Completed: ${params.OBJECTIVE_COMPLETED}" >> reports/status.txt
                    echo "Current Status: ${env.ALERT_MESSAGE}" >> reports/status.txt
                    echo "" >> reports/status.txt
                    echo "Success Criteria:" >> reports/status.txt
                    echo "${env.SUCCESS_CRITERIA}" >> reports/status.txt
                """
            }
        }
        
        stage('Send Notifications') {
            when {
                expression { !params.OBJECTIVE_COMPLETED }
            }
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def reminderTime = config.notification_settings.default_reminder_time
                    
                    echo "Scheduling next reminder for: ${reminderTime}"
                    sh """
                        echo "Notification: ${env.ALERT_MESSAGE}" >> reports/notifications.txt
                        echo "Next reminder scheduled for: ${reminderTime}" >> reports/notifications.txt
                    """
                }
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true
        }
        success {
            script {
                if (params.OBJECTIVE_COMPLETED) {
                    def config = readYaml file: CONFIG_FILE
                    echo "Updating metrics..."
                    sh """
                        echo "Completion recorded for ${params.MONTH}" >> reports/metrics.txt
                        echo "Metrics tracked: ${config.reporting.metrics.join(', ')}" >> reports/metrics.txt
                    """
                }
            }
        }
    }
}