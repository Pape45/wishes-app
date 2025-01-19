pipeline {
    agent any
    
    parameters {
        choice(name: 'MONTH', choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'], description: 'Select month for wish tracking')
        string(name: 'DAY', defaultValue: '1', description: 'Enter current day (1-31)')
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
                    env.SUCCESS_CRITERIA = monthConfig.success_criteria.join('\n')
                    
                    // Create checkboxes for each success criteria
                    def checkboxes = monthConfig.success_criteria.collect { criteria ->
                        booleanParam(defaultValue: false, name: "objective_${criteria.replaceAll(/\s+/, '_')}", description: criteria)
                    }
                    
                    properties([
                        parameters(checkboxes)
                    ])
                }
            }
        }
        
        stage('Display Objectives') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    
                    echo """
                    ╔════════════════════════════════════════════╗
                    ║             MONTHLY OBJECTIVES             ║
                    ╠════════════════════════════════════════════╣
                    ║ Month: ${params.MONTH}
                    ║ Goal: ${monthConfig.name}
                    ║ Description: ${monthConfig.description}
                    ╠════════════════════════════════════════════╣
                    ║ Success Criteria:                          ║"""
                    
                    monthConfig.success_criteria.each { criteria ->
                        def paramName = "objective_${criteria.replaceAll(/\s+/, '_')}"
                        def status = params[paramName] ? '✓' : '✗'
                        echo "║ ${status} ${criteria}"
                    }
                    
                    echo "╚════════════════════════════════════════════╝"
                }
            }
        }
        
        stage('Progress Evaluation') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    def totalObjectives = monthConfig.success_criteria.size()
                    def completedObjectives = monthConfig.success_criteria.count { criteria ->
                        params["objective_${criteria.replaceAll(/\s+/, '_')}"]
                    }
                    
                    def progressPercentage = (completedObjectives / totalObjectives) * 100
                    
                    echo """
                    🎯 PROGRESS REPORT
                    ═══════════════════════════════════
                    Completed: ${completedObjectives}/${totalObjectives} (${progressPercentage}%)
                    """
                    
                    if (progressPercentage < 100) {
                        def day = params.DAY.toInteger()
                        def urgencyLevel = day > 25 ? '⚠️ URGENT' : '📢 REMINDER'
                        echo """
                        ${urgencyLevel}:
                        ${day <= 15 ? monthConfig.reminder_before_15 : monthConfig.reminder_after_15}
                        """
                    } else {
                        echo """
                        🎉 CONGRATULATIONS!
                        You've completed all objectives for ${params.MONTH}!
                        """
                    }
                }
            }
        }
    }
    
    post {
        always {
            script {
                def config = readYaml file: CONFIG_FILE
                def monthConfig = config.objectives[params.MONTH]
                
                // Generate HTML report
                def htmlReport = """
                <html>
                    <body style="font-family: Arial; margin: 20px;">
                        <h1 style="color: #333;">${params.MONTH} Objectives Report</h1>
                        <div style="border: 1px solid #ccc; padding: 20px; border-radius: 5px;">
                            <h2>${monthConfig.name}</h2>
                            <p>${monthConfig.description}</p>
                            <h3>Progress:</h3>
                            <ul>
                """
                
                monthConfig.success_criteria.each { criteria ->
                    def isDone = params["objective_${criteria.replaceAll(/\s+/, '_')}"]
                    htmlReport += """
                    <li style="color: ${isDone ? 'green' : 'red'}">
                        ${isDone ? '✓' : '✗'} ${criteria}
                    </li>
                    """
                }
                
                htmlReport += """
                            </ul>
                        </div>
                    </body>
                </html>
                """
                
                writeFile file: 'reports/report.html', text: htmlReport
                archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true
            }
        }
    }
}