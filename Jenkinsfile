node {
    echo "git branch name: ${env.BRANCH_NAME}"
    echo "build number is: ${env.BUILD_NUMBER}"
    echo "node name is: ${env.NODE_NAME}"
    
    
    // /var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/maven-3.9.16/bin
    def mavenHome=tool name: "maven-3.9.16"
    try {
    stage("SCM Check-out") {
     git branch: 'devlepment', url: 'https://github.com/santoshaws14/maven-web-application.git'
    }
    stage("Compile") {
        sh "${mavenHome}/bin/mvn clean compile"
    }
    stage("Build") {
        sh "${mavenHome}/bin/mvn clean package"
    }
    stage("SQ Report") {
        sh "${mavenHome}/bin/mvn sonar:sonar"
    }
    stage("Upload Artifact") {
        sh "${mavenHome}/bin/mvn clean deploy"
    }
    stage('Uploadarifacttonexus')
   {
  sh "${mavenHome}/bin/mvn clean package sonar:sonar deploy"
   }
    stage('Deploy to Tomcat') {
        echo "Deploying WAR file using curl..."

        sh """
            curl -u admin:password \
            --upload-file /var/lib/jenkins/workspace/MBPL-Jio/target/maven-web-application.war \
            "http://16.113.120.231:8080/manager/text/deploy?path=/maven-web-application&update=true"
        """
    }
}
catch (e) {
   
       currentBuild.result = "FAILED"

  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
    }
}

  def notifyBuild(String buildStatus = 'STARTED') {
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
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary, channel: '#anusha')
  slackSend (color: colorCode, message: summary, channel: '#gopal')
}
