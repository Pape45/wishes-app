pipeline {
    agent any
    
    parameters {
        choice(name: 'MONTH', choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'])
        string(name: 'DAY', defaultValue: '1')
        booleanParam(name: 'TASK_1_COMPLETED', defaultValue: false, description: 'First objective completed')
        booleanParam(name: 'TASK_2_COMPLETED', defaultValue: false, description: 'Second objective completed')
        booleanParam(name: 'TASK_3_COMPLETED', defaultValue: false, description: 'Third objective completed')
    }
    
    environment {
        CONFIG_FILE = 'src/pipelines/objectives-config.yaml'
    }
    
    stages {
        stage('Display Objectives') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    def tasksCompleted = [params.TASK_1_COMPLETED, params.TASK_2_COMPLETED, params.TASK_3_COMPLETED].count { it }
                    
                    echo """
                    ╔═══════════════════════════════════════════════════╗
                    ║                 MONTHLY OBJECTIVES                 ║
                    ╠═══════════════════════════════════════════════════╣
                    ║ Month: ${params.MONTH}
                    ║ Goal: ${monthConfig.name}
                    ╠═══════════════════════════════════════════════════╣
                    ║ Tasks Progress:                                   ║
                    ║ [${params.TASK_1_COMPLETED ? '✓' : ' '}] ${monthConfig.success_criteria[0]}
                    ║ [${params.TASK_2_COMPLETED ? '✓' : ' '}] ${monthConfig.success_criteria[1]}
                    ║ [${params.TASK_3_COMPLETED ? '✓' : ' '}] ${monthConfig.success_criteria[2]}
                    ║                                                   ║
                    ║ Total Progress: ${tasksCompleted}/3 tasks completed
                    ╚═══════════════════════════════════════════════════╝"""
                }
            }
        }
        
        stage('Generate Report') {
            steps {
                script {
                    def config = readYaml file: CONFIG_FILE
                    def monthConfig = config.objectives[params.MONTH]
                    
                    writeFile file: "reports/${params.MONTH}_progress.html", text: """
                    <html>
                        <head>
                            <style>
                                body { font-family: Arial; margin: 20px; }
                                .report { border: 1px solid #ccc; padding: 20px; }
                                .complete { color: green; }
                                .incomplete { color: red; }
                            </style>
                        </head>
                        <body>
                            <div class="report">
                                <h1>${params.MONTH} Progress Report</h1>
                                <h2>${monthConfig.name}</h2>
                                <ul>
                                    <li class="${params.TASK_1_COMPLETED ? 'complete' : 'incomplete'}">
                                        ${monthConfig.success_criteria[0]} - ${params.TASK_1_COMPLETED ? '✓' : '✗'}
                                    </li>
                                    <li class="${params.TASK_2_COMPLETED ? 'complete' : 'incomplete'}">
                                        ${monthConfig.success_criteria[1]} - ${params.TASK_2_COMPLETED ? '✓' : '✗'}
                                    </li>
                                    <li class="${params.TASK_3_COMPLETED ? 'complete' : 'incomplete'}">
                                        ${monthConfig.success_criteria[2]} - ${params.TASK_3_COMPLETED ? '✓' : '✗'}
                                    </li>
                                </ul>
                            </div>
                        </body>
                    </html>
                    """
                    archiveArtifacts artifacts: 'reports/*.html'
                }
            }
        }
    }
}