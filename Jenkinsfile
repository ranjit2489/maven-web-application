node{
    
    def mavenHome = tool name: "maven3.8.4"
	
   properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])
    
   echo "The Job name is: ${env.JOB_NAME}"
   echo "The node name is: ${env.NODE_NAME}"
   echo "Workspace path is: ${env.WORKSPACE}"
   echo "The Node Lebel is: ${env.NODE_LABELS}"
  
try slacknotifications("STARTED"){
	
stage('CheckoutCode'){
git branch: 'development', credentialsId: 'b547e82b-1049-4296-9b5b-08d9f1e3cfe5', url: 'https://github.com/ranjit2489/maven-web-application.git'
}

stage('Build'){
sh "${mavenHome}/bin/mvn clean package"
}
	
/*
stage('ExecuteSonarQubeReport'){
sh "${mavenHome}/bin/mvn sonar:sonar"
}

stage('UploadArtifactsIntoNexus'){
sh "${mavenHome}/bin/mvn deploy"
}

stage('DeployAplicationToTomcatServer'){
sshagent(['16104a0b-d5da-4cca-bc0b-4c1a711fc6c3']) {
    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.232.242.193:/opt/apache-tomcat-9.0.65/webapps/"
}
} */
  
} //try closing	
	
catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILURE"
    throw e
  } 
finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
	
} //Node Closing

def slacknotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"


  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
