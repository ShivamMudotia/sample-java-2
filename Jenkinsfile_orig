pipeline
{
    agent any
    environment {
        PATH = "$PATH:/usr/bin"
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
                    "target": "sonar-artifactory-sample/"
                  }
                 ]
               }''',
 
            )
         }
       }


   }

}
