pipeline {
    agent any

    parameters {
        choice(
            name: 'MONTH', 
            choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
            description: 'Sélectionnez un mois'
        )
        string(
            name: 'DAY', 
            defaultValue: '1', 
            description: 'Jour actuel (1-31)'
        )
    }

    stages {
        stage('Saisie des Tâches') {
            steps {
                script {
                    // Récupérer les tâches du mois sélectionné
                    def monthTasks = getMonthTasks(params.MONTH)

                    // Générer dynamiquement les checkboxes
                    def taskInputs = monthTasks.collectWithIndex { task, index ->
                        booleanParam(
                            name: "TASK${index + 1}", 
                            defaultValue: false, 
                            description: task
                        )
                    }

                    // Saisie utilisateur avec les checkboxes dynamiques
                    def taskResponses = input(
                        id: 'TaskSelection', 
                        message: 'Cochez les tâches accomplies', 
                        parameters: taskInputs
                    )

                    // Stocker les réponses dans des variables
                    env.TASK1 = taskResponses.TASK1
                    env.TASK2 = taskResponses.TASK2
                    env.TASK3 = taskResponses.TASK3
                }
            }
        }

        stage('Afficher la Progression') {
            steps {
                script {
                    def monthTasks = getMonthTasks(params.MONTH)
                    def tasksCompleted = [env.TASK1.toBoolean(), env.TASK2.toBoolean(), env.TASK3.toBoolean()]

                    echo """
                    ╔═══════════════════════════════════════════════════╗
                    ║                 ${params.MONTH} OBJECTIVES              ║
                    ╠═══════════════════════════════════════════════════╣
                    ║ [${tasksCompleted[0] ? '✓' : ' '}] ${monthTasks[0].padEnd(40)} ║
                    ║ [${tasksCompleted[1] ? '✓' : ' '}] ${monthTasks[1].padEnd(40)} ║
                    ║ [${tasksCompleted[2] ? '✓' : ' '}] ${monthTasks[2].padEnd(40)} ║
                    ╠═══════════════════════════════════════════════════╣
                    ║ Complétés: ${tasksCompleted.count { it }}/3 tâches      ║
                    ╚═══════════════════════════════════════════════════╝
                    """
                }
            }
        }

        stage('Générer le Rapport') {
            steps {
                script {
                    def monthTasks = getMonthTasks(params.MONTH)
                    def tasksCompleted = [env.TASK1.toBoolean(), env.TASK2.toBoolean(), env.TASK3.toBoolean()]

                    def htmlReport = """
                    <html>
                        <head>
                            <style>
                                body { font-family: Arial; margin: 20px; }
                                .task-complete { color: #4CAF50; }
                                .task-incomplete { color: #f44336; }
                                .progress-bar { 
                                    width: 100%; 
                                    background-color: #f0f0f0; 
                                    border-radius: 5px;
                                }
                                .progress-fill {
                                    height: 20px;
                                    background-color: #4CAF50;
                                    border-radius: 5px;
                                }
                            </style>
                        </head>
                        <body>
                            <h1>Rapport de Progression - ${params.MONTH}</h1>
                            <div class="progress-bar">
                                <div class="progress-fill" style="width: ${(tasksCompleted.count { it } / 3 * 100)}%"></div>
                            </div>
                            <h3>Tâches :</h3>
                            <ul>
                                ${monthTasks.collectWithIndex { task, index -> 
                                    "<li class='${tasksCompleted[index] ? "task-complete" : "task-incomplete"}'>${task} ${tasksCompleted[index] ? "✓" : "◻"}</li>"
                                }.join("")}
                            </ul>
                            <p>Généré le : ${new Date().format('dd/MM/yyyy HH:mm')}</p>
                        </body>
                    </html>
                    """

                    writeFile(file: "rapport_${params.MONTH}.html", text: htmlReport)
                    archiveArtifacts artifacts: "rapport_${params.MONTH}.html"
                }
            }
        }
    }
}

def getMonthTasks(month) {
    return [
        'January'   : ['Entrées quotidiennes journalisées', 'Pensées documentées', 'Réflexions enregistrées'],
        'February'  : ['Espace réorganisé', 'Améliorations documentées', 'Changements implémentés'],
        'March'     : ['Exercice physique terminé', 'Repas sains préparés', 'Méditation pratiquée'],
        'April'     : ['Actions bienveillantes quotidiennes', 'Interactions positives', 'Aide apportée à autrui'],
        'May'       : ['Nouvelle activité tentée', 'Compétences apprises', 'Zone de confort élargie'],
        'June'      : ['Activités familiales complétées', 'Temps de qualité passé', 'Souvenirs créés'],
        'July'      : ['Événements participés', 'Danses exécutées', 'Musique appréciée'],
        'August'    : ['Visite à la plage planifiée', 'Expérience nocturne complétée', 'Souvenirs documentés'],
        'September' : ['Nouveaux lieux visités', 'Expériences documentées', 'Horizons élargis'],
        'October'   : ['Sessions d\'étude complétées', 'Connaissances acquises', 'Progrès documentés'],
        'November'  : ['Sessions de pratique complétées', 'Compétences améliorées', 'Chansons apprises'],
        'December'  : ['Pratique quotidienne complétée', 'Moments de pleine conscience enregistrés', 'Progrès suivis']
    ].get(month, ['Tâche 1', 'Tâche 2', 'Tâche 3'])
}