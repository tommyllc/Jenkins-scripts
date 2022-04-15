# Jenkins-scripts
Groovy for pipeline
//SCRIPTED PIPELINE
//the 3basic steps that takes place in a jenkinsfile or PIPELINE is build, test and deploy
node{
    def MHD = tool name: 'maven3.8.5'
    stage('1.CloneCode'){
        sh "echo cloning src from our project repo"
        git 'https://github.com/Pro-Fintec/package-management.git'
    }
    stage('2.Build'){
        sh "echo validating, compilation, testing and generate a package"
        sh "${MHD}/bin/mvn clean package"
    }
    stage('3.CodeQuality'){
        sh "echo performing code quality report"
        sh "${MHD}/bin/mvn sonar:sonar"
    }
    stage('4.UploadArtifact'){
        sh "echo uploading artifact into Nexus"
        sh "${MHD}/bin/mvn deploy"
    }
    stage('5.Deploy2tomcat'){
        sh "echo deploying artifact into tomcat"
        }
      stage('6.Deploy2uat'){
        sshagent(['agentcredentials']) {
}  
      }
     stage('7.approval'){
      timeout(time:8, unit:'HOURS'){
        input message: 'Please approve deployment to Production'
      }
     }
   stage('8.Deploy2prod'){
        sshagent(['agentcredentials']) {
        sh "scp -o StrictHostKeyChecking=no target/*.war ec2-user@172.31.89.218:/opt/tomcat9/webapps/app.war"
        
}      
      }

  // stage('9.EmailAlerts'){
    emailext body: '''Hi

Build status for boa app.

Regards,
Landmark Technologies''', recipientProviders: [developers(), requestor()], subject: 'Project status', to: 'boa@gmail.com'
   
