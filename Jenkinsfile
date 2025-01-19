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
        stage('Initialize') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    
                    // Create dynamic parameters for objectives
                    def parametersList = []
                    parametersList.add(
                        choice(name: 'MONTH', choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'])
                    )
                    parametersList.add(
                        string(name: 'DAY', defaultValue: params.DAY)
                    )
                    
                    monthConfig.success_criteria.each { criteria ->
                        parametersList.add(
                            booleanParam(
                                name: "OBJECTIVE_${criteria.replaceAll(/\s+/, '_')}",
                                defaultValue: false,
                                description: "Completed: ${criteria}"
                            )
                        )
                    }
                    
                    properties([
                        parameters(parametersList)
                    ])
                }
            }
        }
        
        stage('Validate Inputs') {
            steps {
                script {
                    def day = params.DAY.toInteger()
                    if (day < 1 || day > 31) {
                        error "Invalid day value: ${day}. Must be between 1 and 31."
                    }
                }
            }
        }
        
        stage('Display Current Objectives') {
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
                        def paramName = "OBJECTIVE_${criteria.replaceAll(/\s+/, '_')}"
                        def status = params[paramName] ? '✅' : '❌'
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
                    def totalObjectives = monthConfig.success_criteria.size()
                    def completedObjectives = monthConfig.success_criteria.count { criteria ->
                        params["OBJECTIVE_${criteria.replaceAll(/\s+/, '_')}"]
                    }
                    
                    def progressPercentage = (completedObjectives / totalObjectives) * 100
                    def day = params.DAY.toInteger()
                    
                    echo """
                    🎯 PROGRESS SUMMARY
                    ═══════════════════════════════════════════════════
                    Completed: ${completedObjectives}/${totalObjectives} (${progressPercentage}%)
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
                    
                    def completed = 0
                    monthConfig.success_criteria.each { criteria ->
                        def isDone = params["OBJECTIVE_${criteria.replaceAll(/\s+/, '_')}"]
                        completed += isDone ? 1 : 0
                        htmlReport += """
                        <li class="${isDone ? 'complete' : 'incomplete'}">
                            ${isDone ? '✓' : '✗'} ${criteria}
                        </li>
                        """
                    }
                    
                    def progress = (completed / monthConfig.success_criteria.size()) * 100
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
            ✨ Pipeline completed successfully!
            📊 View detailed report in Jenkins artifacts
            """
        }
        failure {
            echo """
            ❌ Pipeline failed!
            Please check the logs for details
            """
        }
    }
}