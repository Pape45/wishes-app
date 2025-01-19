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
        stage('Load Objectives') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    
                    // Store configuration for later use
                    env.MONTH_NAME = monthConfig.name
                    env.MONTH_DESCRIPTION = monthConfig.description
                    
                    // Create input for objectives
                    def checkboxes = [:]
                    monthConfig.success_criteria.each { criteria ->
                        checkboxes[criteria] = false
                    }
                    
                    // Prompt user for objective completion
                    def userInput = input(
                        id: 'objectiveInput',
                        message: "Mark completed objectives for ${params.MONTH}:",
                        parameters: monthConfig.success_criteria.collect { criteria ->
                            booleanParam(
                                defaultValue: false,
                                name: criteria.replaceAll(/\s+/, '_'),
                                description: criteria
                            )
                        }
                    )
                    
                    // Store results
                    env.COMPLETED_OBJECTIVES = userInput.findAll { it.value }.keySet().join(',')
                    env.TOTAL_OBJECTIVES = monthConfig.success_criteria.size().toString()
                }
            }
        }
        
        stage('Display Progress') {
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
                    ║ Current Progress:                                 ║"""
                    
                    monthConfig.success_criteria.each { criteria ->
                        def isCompleted = env.COMPLETED_OBJECTIVES.contains(criteria.replaceAll(/\s+/, '_'))
                        def status = isCompleted ? '✅' : '❌'
                        echo "║ ${status} ${criteria}"
                    }
                    
                    echo "╚═══════════════════════════════════════════════════╝"
                }
            }
        }
        
        stage('Evaluate Progress') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    def completedCount = env.COMPLETED_OBJECTIVES ? env.COMPLETED_OBJECTIVES.split(',').size() : 0
                    def totalCount = env.TOTAL_OBJECTIVES.toInteger()
                    def progressPercentage = (completedCount / totalCount) * 100
                    def day = params.DAY.toInteger()
                    
                    echo """
                    🎯 PROGRESS SUMMARY
                    ═══════════════════════════════════════════════════
                    Completed: ${completedCount}/${totalCount} (${progressPercentage}%)
                    Day: ${day}/31
                    
                    ${progressPercentage < 100 ? '''
                    ''' + (day > 25 ? '⚠️ URGENT: Time is running out!' : '📢 Keep going!') : '''
                    🎉 CONGRATULATIONS! All objectives completed!
                    '''}
                    """
                    
                    if (progressPercentage < 100) {
                        echo monthConfig[day <= 15 ? 'reminder_before_15' : 'reminder_after_15']
                    }
                }
            }
        }
        
        stage('Generate Report') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    
                    // Create HTML report
                    def htmlReport = """
                    <html>
                        <head>
                            <style>
                                body { font-family: Arial; margin: 20px; }
                                .report { border: 1px solid #ccc; padding: 20px; border-radius: 5px; }
                                .complete { color: green; }
                                .incomplete { color: red; }
                                .progress-bar { 
                                    background-color: #f0f0f0;
                                    border-radius: 5px;
                                    padding: 3px;
                                }
                                .progress-fill {
                                    background-color: #4CAF50;
                                    height: 20px;
                                    border-radius: 5px;
                                    width: %PROGRESS%%;
                                }
                            </style>
                        </head>
                        <body>
                            <div class="report">
                                <h1>${params.MONTH} Progress Report</h1>
                                <h2>${monthConfig.name}</h2>
                                <p>${monthConfig.description}</p>
                                
                                <div class="progress-bar">
                                    <div class="progress-fill"></div>
                                </div>
                                
                                <h3>Objectives:</h3>
                                <ul>
                    """
                    
                    monthConfig.success_criteria.each { criteria ->
                        def isCompleted = env.COMPLETED_OBJECTIVES.contains(criteria.replaceAll(/\s+/, '_'))
                        htmlReport += """
                        <li class="${isCompleted ? 'complete' : 'incomplete'}">
                            ${isCompleted ? '✓' : '✗'} ${criteria}
                        </li>
                        """
                    }
                    
                    def completedCount = env.COMPLETED_OBJECTIVES ? env.COMPLETED_OBJECTIVES.split(',').size() : 0
                    def progress = (completedCount / env.TOTAL_OBJECTIVES.toInteger()) * 100
                    htmlReport = htmlReport.replace('%PROGRESS%', "${progress}")
                    
                    htmlReport += """
                                </ul>
                            </div>
                        </body>
                    </html>
                    """
                    
                    writeFile file: 'reports/progress.html', text: htmlReport
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