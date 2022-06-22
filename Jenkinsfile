def SendEmailNotification(String result) {
  
    //config 
    // def to = emailextrecipients([
    //      "shivam.mudotia@nagarro.com;nagender.singh@nagarro.com"
    // ])
    //def to = "shivam.mudotia@nagarro.com;nagender.singh@nagarro.com"
    // set variables
    // def subject = "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} ${result}"
    // def content = '${JELLY_SCRIPT,template="html"}'

    // send email
    if(to != null && !to.isEmpty()) {
      emailext(body: body, mimeType: 'text/html',
         subject: subject,
         to: to, attachLog: true )
    }
}

pipeline
{
    agent any
    environment {
        PATH = "$PATH:/usr/bin"
        //email_to = "shivam.mudotia@nagarro.com;nagender.singh@nagarro.com"
        email_to = "shivam.mudotia@nagarro.com"
    }
    
    stages
    {

       stage('Build')
       {
            steps
            {
              withMaven(mavenSettingsConfig: 'artifactory-maven') {
                  sh "mvn clean verify"
                  sh 'mvn clean package'
                }
            }    
       }
        

       stage('SonarQube analysis') 
       {
          steps
          {
              withSonarQubeEnv('sonarqube') { 
              sh "mvn sonar:sonar"
              }
          }
       }
     
       
       stage('Push Artifacts to Artifactory')
       {
          steps
          {
            rtUpload (
               serverId: 'artifactory',
               spec: '''{
                  "files": [
                  {
                    "pattern": "*.war",
                    "target": "app2/sonar-artifactory-sample-3/"
                  }
                 ]
               }''',
 
            )
         }
       }

        
       stage ('Publish build info') 
       {
           steps 
           {
              rtPublishBuildInfo (
                 serverId: 'artifactory'
              )
           }
        }

    }
    
    post {
       always {
           emailext body: 'Check detailed console output at below URL. \n $BUILD_URL \n\n Code Commits \n\n ${CHANGES} \n\n -------------------------------------------------- \n\n Build Logs (Truncated) - Full Logs Attached \n\n${BUILD_LOG, maxLines=100, escapeHtml=false}', 
           to: "${email_to}", 
           subject: '$BUILD_STATUS : $PROJECT_NAME - #$BUILD_NUMBER',
           attachLog: true
       }
    }

}
