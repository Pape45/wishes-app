pipeline {
    agent any
    
    parameters {
        choice(name: 'MONTH', choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'], description: 'Select month for wish tracking')
        string(name: 'DAY', defaultValue: '1', description: 'Enter current day (1-31)')
        booleanParam(name: 'ALL_OBJECTIVES_COMPLETED', defaultValue: false, description: 'Mark if all objectives for the month are completed')
    }
    
    environment {
        ALERT_MESSAGE = ''
        CONFIG_FILE = 'src/pipelines/objectives-config.yaml'
    }
    
    stages {
        stage('Display Objectives') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    
                    echo """
                    ╔═══════════════════════════════════════════════════╗
                    ║                 MONTHLY OBJECTIVES                 ║
                    ╠═══════════════════════════════════════════════════╣
                    ║ Month: ${params.MONTH}
                    ║ Goal: ${monthConfig.name}
                    ║ Description: ${monthConfig.description}
                    ╠═══════════════════════════════════════════════════╣
                    ║ Required Objectives:                              ║"""
                    
                    monthConfig.success_criteria.each { criteria ->
                        echo "║ • ${criteria}"
                    }
                    
                    echo """║                                                   ║
                    ║ Status: ${params.ALL_OBJECTIVES_COMPLETED ? '✅ Completed' : '❌ Not Completed'}
                    ╚═══════════════════════════════════════════════════╝"""
                }
            }
        }
        
        stage('Evaluate Progress') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    def day = params.DAY.toInteger()
                    
                    if (!params.ALL_OBJECTIVES_COMPLETED) {
                        echo """
                        🎯 PROGRESS ALERT
                        ═══════════════════════════════════════════════════
                        ${day > 25 ? '⚠️ URGENT: Time is running out!' : '📢 Keep going!'}
                        
                        ${monthConfig[day <= 15 ? 'reminder_before_15' : 'reminder_after_15']}
                        """
                    } else {
                        echo """
                        🎉 CONGRATULATIONS!
                        ═══════════════════════════════════════════════════
                        All objectives for ${params.MONTH} completed!
                        """
                    }
                }
            }
        }
        
        stage('Generate Report') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    
                    writeFile file: 'reports/progress.html', text: """
                    <html>
                        <head>
                            <style>
                                body { font-family: Arial; margin: 20px; }
                                .report { border: 1px solid #ccc; padding: 20px; border-radius: 5px; }
                                .complete { color: green; }
                                .incomplete { color: red; }
                            </style>
                        </head>
                        <body>
                            <div class="report">
                                <h1>${params.MONTH} Progress Report</h1>
                                <h2>${monthConfig.name}</h2>
                                <p>${monthConfig.description}</p>
                                <h3>Status: <span class="${params.ALL_OBJECTIVES_COMPLETED ? 'complete' : 'incomplete'}">
                                    ${params.ALL_OBJECTIVES_COMPLETED ? '✓ Completed' : '✗ Not Completed'}
                                </span></h3>
                                <h3>Required Objectives:</h3>
                                <ul>
                                    ${monthConfig.success_criteria.collect { "<li>$it</li>" }.join('')}
                                </ul>
                            </div>
                        </body>
                    </html>
                    """
                    
                    archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true
                }
            }
        }
    }
    
    post {
        success {
            echo """
            ═══════════════════════════════════════════════════
                    ✨ Pipeline completed successfully!
                    📊 View detailed report in Jenkins artifacts
            ═══════════════════════════════════════════════════
            """
        }
        failure {
            echo """
            ═══════════════════════════════════════════════════
                    ❌ Pipeline failed!
                    Please check the logs for details
            ═══════════════════════════════════════════════════
            """
        }
    }
}