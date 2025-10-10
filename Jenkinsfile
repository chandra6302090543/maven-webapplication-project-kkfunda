node {
  echo "git branch name: ${env.BRANCH_NAME}"
  echo "build number is: ${env.BUILD_NUMBER}"
  echo "node name is: ${env.NODE_NAME}"

  def mavenHome = tool name: "maven_3.9.11"

  try {
    stage('Git Checkout') {
      notifyBuild('STARTED')
      git branch: 'development', url: 'https://github.com/chandra6302090543/maven-webapplication-project-kkfunda.git'
    }

    stage('Compile') {
      sh "${mavenHome}/bin/mvn clean compile"
    }

    stage('Build') {
      sh "${mavenHome}/bin/mvn clean package"
    }

    stage('SonarQube Report') {
      sh "${mavenHome}/bin/mvn sonar:sonar"
    }

    stage('Upload Artifact') {
      sh "${mavenHome}/bin/mvn clean deploy"
    }

    stage('Deploy to Tomcat') {
      sh """
        curl -u admin:password \\
        --upload-file /var/lib/jenkins/workspace/scripted-pipeline/target/maven-web-application.war \\
        "http://65.2.150.106:8080/manager/text/deploy?path=/maven-web-application&update=true"
      """
    }

    currentBuild.result = 'SUCCESS'
  } catch (e) {
    currentBuild.result = 'FAILURE'
    error "Build failed: ${e.message}"
  } finally {
    notifyBuild(currentBuild.result)
  }
}

def notifyBuild(String buildStatus = 'STARTED') {
  buildStatus = buildStatus ?: 'SUCCESS'

  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  if (buildStatus == 'STARTED') {
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    colorCode = '#00FF00'
  }

  slackSend(color: colorCode, message: summary, channel: '#jio-dev')
  slackSend(color: colorCode, message: summary, channel: '#jio-devops')
}
