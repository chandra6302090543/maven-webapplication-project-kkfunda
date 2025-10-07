node {
    // Properly define Maven tool with type
    def mavenHome = tool name: 'maven_3.9.11', type: 'maven'

    stage('Git Checkout') {
        git branch: 'development', url: 'https://github.com/chandra6302090543/maven-webapplication-project-kkfunda.git'
    }

    stage('Compile') {
        sh "${mavenHome}/bin/mvn compile"
    }

    stage('Build') {
        sh "${mavenHome}/bin/mvn clean package"
    }

    stage('SQ Report') {
        sh "${mavenHome}/bin/mvn sonar:sonar"
    }

    stage('Deploy into Nexus') {
        sh "${mavenHome}/bin/mvn clean deploy"
    }
    stage('Deploy to Tomcat') {
    echo "Deploying WAR file using curl..."

    sh """
        curl -u admin:password \
        --upload-file /var/lib/jenkins/workspace/scripted-pipeline/target/maven-web-application.war \
        "http://13.126.229.17:8080/manager/text/deploy?path=/maven-web-application&update=true"
    """
}

}
