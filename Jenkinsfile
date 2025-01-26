pipeline {
   agent any
   
   parameters {
       choice(name: 'MONTH', choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'])
       string(name: 'DAY', defaultValue: '1', description: 'Current day (1-31)')
       booleanParam(name: 'TASK1', defaultValue: false, description: 'Task 1')
       booleanParam(name: 'TASK2', defaultValue: false, description: 'Task 2')
       booleanParam(name: 'TASK3', defaultValue: false, description: 'Task 3')
   }

   stages {
       stage('Initialize') {
           steps {
               script {
                   def monthTasks = getMonthTasks(params.MONTH)
                   currentBuild.description = "Month: ${params.MONTH}"
                   
                   // Update task descriptions
                   def tasks = []
                   monthTasks.eachWithIndex { task, index ->
                       tasks.add(
                           booleanParam(
                               name: "TASK${index + 1}", 
                               defaultValue: false, 
                               description: task
                           )
                       )
                   }
                   
                   properties([
                       parameters([
                           choice(name: 'MONTH', choices: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']),
                           string(name: 'DAY', defaultValue: '1', description: 'Current day (1-31)'),
                           tasks
                       ].flatten())
                   ])
               }
           }
       }

       stage('Display Progress') {
           steps {
               script {
                   def monthTasks = getMonthTasks(params.MONTH)
                   def taskStatus = [params.TASK1, params.TASK2, params.TASK3]
                   
                   echo """
                   ╔═══════════════════════════════════════════════════╗
                   ║                 ${params.MONTH} OBJECTIVES              ║
                   ╠═══════════════════════════════════════════════════╣
                   ║ Progress:                                         ║
                   ║ [${taskStatus[0] ? '✓' : ' '}] ${monthTasks[0]}
                   ║ [${taskStatus[1] ? '✓' : ' '}] ${monthTasks[1]}
                   ║ [${taskStatus[2] ? '✓' : ' '}] ${monthTasks[2]}
                   ║                                                   ║
                   ║ Completed: ${taskStatus.count{it}}/3 tasks       ║
                   ╚═══════════════════════════════════════════════════╝
                   """
               }
           }
       }
       
       stage('Generate Report') {
           steps {
               script {
                   def monthTasks = getMonthTasks(params.MONTH)
                   def taskStatus = [params.TASK1, params.TASK2, params.TASK3]
                   
                   def taskListHtml = ''
                   for (int i = 0; i < monthTasks.size(); i++) {
                       taskListHtml += """<li class='${taskStatus[i] ? 'complete' : 'incomplete'}'>
                           ${monthTasks[i]} - ${taskStatus[i] ? '✓' : '◻'}
                       </li>"""
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
                               <div class="progress-bar">
                                   <div class="progress" style="width: ${(taskStatus.count{it}/3)*100}%"></div>
                               </div>
                               <h3>Tasks:</h3>
                               <ul>${taskListHtml}</ul>
                               <p>Generated: ${new Date().format('yyyy-MM-dd HH:mm:ss')}</p>
                           </div>
                       </body>
                   </html>
                   """
                   archiveArtifacts artifacts: "reports/${params.MONTH}_progress.html"
               }
           }
       }
   }
}

def getMonthTasks(month) {
   switch(month) {
       case 'January':
           return ['Daily entries logged', 'Thoughts documented', 'Reflections recorded']
       case 'February':
           return ['Space reorganized', 'Improvements documented', 'Changes implemented']
       case 'March':
           return ['Exercise completed', 'Healthy meals prepared', 'Meditation practiced']
       case 'April':
           return ['Daily kind actions', 'Positive interactions', 'Help offered to others']
       case 'May':
           return ['New activity attempted', 'Skills learned', 'Comfort zone expanded']
       case 'June':
           return ['Family activities completed', 'Quality time spent', 'Memories created']
       case 'July':
           return ['Events attended', 'Dances participated', 'Music enjoyed']
       case 'August':
           return ['Beach visit planned', 'Night experience completed', 'Memories documented']
       case 'September':
           return ['New places visited', 'Experiences documented', 'Horizons expanded']
       case 'October':
           return ['Study sessions completed', 'Knowledge gained', 'Progress documented']
       case 'November':
           return ['Practice sessions completed', 'Skills improved', 'Songs learned']
       case 'December':
           return ['Daily practice completed', 'Mindful moments recorded', 'Progress tracked']
       default:
           return ['Task 1', 'Task 2', 'Task 3']
   }
}