pipeline {
   agent any
   
   parameters {
       choice(name: 'MONTH', choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'])
       string(name: 'DAY', defaultValue: '1', description: 'Current day (1-31)')
       // Dynamic task parameters for each month
       booleanParam(name: 'JANUARY_TASK1', defaultValue: false, description: 'Daily entries logged')
       booleanParam(name: 'JANUARY_TASK2', defaultValue: false, description: 'Thoughts documented')
       booleanParam(name: 'JANUARY_TASK3', defaultValue: false, description: 'Reflections recorded')
       
       booleanParam(name: 'FEBRUARY_TASK1', defaultValue: false, description: 'Space reorganized')
       booleanParam(name: 'FEBRUARY_TASK2', defaultValue: false, description: 'Improvements documented')
       booleanParam(name: 'FEBRUARY_TASK3', defaultValue: false, description: 'Changes implemented')
       
       booleanParam(name: 'MARCH_TASK1', defaultValue: false, description: 'Exercise completed')
       booleanParam(name: 'MARCH_TASK2', defaultValue: false, description: 'Healthy meals prepared')
       booleanParam(name: 'MARCH_TASK3', defaultValue: false, description: 'Meditation practiced')
       
       booleanParam(name: 'APRIL_TASK1', defaultValue: false, description: 'Daily kind actions')
       booleanParam(name: 'APRIL_TASK2', defaultValue: false, description: 'Positive interactions')
       booleanParam(name: 'APRIL_TASK3', defaultValue: false, description: 'Help offered to others')
       
       booleanParam(name: 'MAY_TASK1', defaultValue: false, description: 'New activity attempted')
       booleanParam(name: 'MAY_TASK2', defaultValue: false, description: 'Skills learned')
       booleanParam(name: 'MAY_TASK3', defaultValue: false, description: 'Comfort zone expanded')
       
       booleanParam(name: 'JUNE_TASK1', defaultValue: false, description: 'Family activities completed')
       booleanParam(name: 'JUNE_TASK2', defaultValue: false, description: 'Quality time spent')
       booleanParam(name: 'JUNE_TASK3', defaultValue: false, description: 'Memories created')
       
       booleanParam(name: 'JULY_TASK1', defaultValue: false, description: 'Events attended')
       booleanParam(name: 'JULY_TASK2', defaultValue: false, description: 'Dances participated')
       booleanParam(name: 'JULY_TASK3', defaultValue: false, description: 'Music enjoyed')
       
       booleanParam(name: 'AUGUST_TASK1', defaultValue: false, description: 'Beach visit planned')
       booleanParam(name: 'AUGUST_TASK2', defaultValue: false, description: 'Night experience completed')
       booleanParam(name: 'AUGUST_TASK3', defaultValue: false, description: 'Memories documented')
       
       booleanParam(name: 'SEPTEMBER_TASK1', defaultValue: false, description: 'New places visited')
       booleanParam(name: 'SEPTEMBER_TASK2', defaultValue: false, description: 'Experiences documented')
       booleanParam(name: 'SEPTEMBER_TASK3', defaultValue: false, description: 'Horizons expanded')
       
       booleanParam(name: 'OCTOBER_TASK1', defaultValue: false, description: 'Study sessions completed')
       booleanParam(name: 'OCTOBER_TASK2', defaultValue: false, description: 'Knowledge gained')
       booleanParam(name: 'OCTOBER_TASK3', defaultValue: false, description: 'Progress documented')
       
       booleanParam(name: 'NOVEMBER_TASK1', defaultValue: false, description: 'Practice sessions completed')
       booleanParam(name: 'NOVEMBER_TASK2', defaultValue: false, description: 'Skills improved')
       booleanParam(name: 'NOVEMBER_TASK3', defaultValue: false, description: 'Songs learned')
       
       booleanParam(name: 'DECEMBER_TASK1', defaultValue: false, description: 'Daily practice completed')
       booleanParam(name: 'DECEMBER_TASK2', defaultValue: false, description: 'Mindful moments recorded')
       booleanParam(name: 'DECEMBER_TASK3', defaultValue: false, description: 'Progress tracked')
   }
   
   environment {
       CONFIG_FILE = 'src/pipelines/objectives-config.yaml'
   }
   
   stages {
       stage('Display Progress') {
           steps {
               script {
                   def config = readYaml file: CONFIG_FILE
                   def monthConfig = config.objectives[params.MONTH]
                   def taskStatus = []
                   
                   switch(params.MONTH) {
                       case 'January':
                           taskStatus = [params.JANUARY_TASK1, params.JANUARY_TASK2, params.JANUARY_TASK3]
                           break
                       case 'February':
                           taskStatus = [params.FEBRUARY_TASK1, params.FEBRUARY_TASK2, params.FEBRUARY_TASK3]
                           break
                       case 'March':
                           taskStatus = [params.MARCH_TASK1, params.MARCH_TASK2, params.MARCH_TASK3]
                           break
                       case 'April':
                           taskStatus = [params.APRIL_TASK1, params.APRIL_TASK2, params.APRIL_TASK3]
                           break
                       case 'May':
                           taskStatus = [params.MAY_TASK1, params.MAY_TASK2, params.MAY_TASK3]
                           break
                       case 'June':
                           taskStatus = [params.JUNE_TASK1, params.JUNE_TASK2, params.JUNE_TASK3]
                           break
                       case 'July':
                           taskStatus = [params.JULY_TASK1, params.JULY_TASK2, params.JULY_TASK3]
                           break
                       case 'August':
                           taskStatus = [params.AUGUST_TASK1, params.AUGUST_TASK2, params.AUGUST_TASK3]
                           break
                       case 'September':
                           taskStatus = [params.SEPTEMBER_TASK1, params.SEPTEMBER_TASK2, params.SEPTEMBER_TASK3]
                           break
                       case 'October':
                           taskStatus = [params.OCTOBER_TASK1, params.OCTOBER_TASK2, params.OCTOBER_TASK3]
                           break
                       case 'November':
                           taskStatus = [params.NOVEMBER_TASK1, params.NOVEMBER_TASK2, params.NOVEMBER_TASK3]
                           break
                       case 'December':
                           taskStatus = [params.DECEMBER_TASK1, params.DECEMBER_TASK2, params.DECEMBER_TASK3]
                           break
                   }
                   
                   def completedTasks = taskStatus.count { it }
                   
                   echo """
                   ╔═══════════════════════════════════════════════════╗
                   ║                 ${params.MONTH} OBJECTIVES              ║
                   ╠═══════════════════════════════════════════════════╣
                   ║ Goal: ${monthConfig.name}
                   ║ Description: ${monthConfig.description}
                   ╠═══════════════════════════════════════════════════╣
                   ║ Progress:                                         ║
                   ║ [${taskStatus[0] ? '✓' : ' '}] ${monthConfig.success_criteria[0]}
                   ║ [${taskStatus[1] ? '✓' : ' '}] ${monthConfig.success_criteria[1]}
                   ║ [${taskStatus[2] ? '✓' : ' '}] ${monthConfig.success_criteria[2]}
                   ║                                                   ║
                   ║ Completed: ${completedTasks}/3 tasks                        ║
                   ╚═══════════════════════════════════════════════════╝
                   """
               }
           }
       }
       
       stage('Generate Report') {
           steps {
               script {
                   def config = readYaml file: CONFIG_FILE
                   def monthConfig = config.objectives[params.MONTH]
                   def taskStatus = []
                   
                   switch(params.MONTH) {
                       case 'January':
                           taskStatus = [params.JANUARY_TASK1, params.JANUARY_TASK2, params.JANUARY_TASK3]
                           break
                       case 'February':
                           taskStatus = [params.FEBRUARY_TASK1, params.FEBRUARY_TASK2, params.FEBRUARY_TASK3]
                           break
                       case 'March':
                           taskStatus = [params.MARCH_TASK1, params.MARCH_TASK2, params.MARCH_TASK3]
                           break
                       case 'April':
                           taskStatus = [params.APRIL_TASK1, params.APRIL_TASK2, params.APRIL_TASK3]
                           break
                       case 'May':
                           taskStatus = [params.MAY_TASK1, params.MAY_TASK2, params.MAY_TASK3]
                           break
                       case 'June':
                           taskStatus = [params.JUNE_TASK1, params.JUNE_TASK2, params.JUNE_TASK3]
                           break
                       case 'July':
                           taskStatus = [params.JULY_TASK1, params.JULY_TASK2, params.JULY_TASK3]
                           break
                       case 'August':
                           taskStatus = [params.AUGUST_TASK1, params.AUGUST_TASK2, params.AUGUST_TASK3]
                           break
                       case 'September':
                           taskStatus = [params.SEPTEMBER_TASK1, params.SEPTEMBER_TASK2, params.SEPTEMBER_TASK3]
                           break
                       case 'October':
                           taskStatus = [params.OCTOBER_TASK1, params.OCTOBER_TASK2, params.OCTOBER_TASK3]
                           break
                       case 'November':
                           taskStatus = [params.NOVEMBER_TASK1, params.NOVEMBER_TASK2, params.NOVEMBER_TASK3]
                           break
                       case 'December':
                           taskStatus = [params.DECEMBER_TASK1, params.DECEMBER_TASK2, params.DECEMBER_TASK3]
                           break
                   }
                   
                   def taskListHtml = ''
                   def i = 0
                   monthConfig.success_criteria.each { task ->
                       taskListHtml += "<li class='${taskStatus[i] ? 'complete' : 'incomplete'}'>${task} - ${taskStatus[i] ? '✓ Completed' : '◻ Pending'}</li>"
                       i++
                   }
                   
                   writeFile file: "reports/${params.MONTH}_progress.html", text: """
                   <html>
                       <head>
                           <style>
                               body { font-family: Arial; margin: 20px; }
                               .report { border: 1px solid #ccc; padding: 20px; }
                               .complete { color: green; }
                               .incomplete { color: red; }
                               .progress-bar { width: 100%; background-color: #f0f0f0; }
                               .progress { background-color: #4CAF50; height: 20px; }
                           </style>
                       </head>
                       <body>
                           <div class="report">
                               <h1>${params.MONTH} Progress Report</h1>
                               <h2>${monthConfig.name}</h2>
                               <p>${monthConfig.description}</p>
                               
                               <div class="progress-bar">
                                   <div class="progress" style="width: ${(taskStatus.count { it } / 3) * 100}%"></div>
                               </div>
                               
                               <h3>Tasks:</h3>
                               <ul>
                                   ${taskListHtml}
                               </ul>
                               
                               <p>Generated on: ${new Date().format('yyyy-MM-dd HH:mm:ss')}</p>
                           </div>
                       </body>
                   </html>
                   """
                   
                   archiveArtifacts artifacts: "reports/${params.MONTH}_progress.html"
               }
           }
       }
   }
   
   post {
       success {
           echo """
           ═══════════════════════════════════════════════════
                   ✨ Pipeline completed successfully!
                   📊 Report generated in Jenkins artifacts
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