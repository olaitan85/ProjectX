pipeline {
  agent any

  tools {
  maven 'Maven3'
  }
  stages {
    stage ('Build') {
      steps {
      sh 'mvn clean install -f MyWebApp/pom.xml'
      }
    }
     stage ('Code Quality') {
      steps {
        withSonarQubeEnv('SonarQube') {
        sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
        }
      }
    }
    stage ('JaCoCo') {
      steps {
      jacoco()
      }
    }
    stage ('Nexus Upload') {
      steps {
      nexusArtifactUploader(
      nexusVersion: 'nexus3',
      protocol: 'http',
      nexusUrl: '54.196.24.192:8081',
      groupId: 'myGroupId',
      version: '1.0-SNAPSHOT',
      repository: 'maven-snapshots',
      credentialsId: '317d5ad3-2b2c-4ea4-b716-db3d218d447f',
      artifacts: [
      [artifactId: 'MyWebApp',
      classifier: '',
      file: 'MyWebApp/target/MyWebApp.war',
      type: 'war']
      ])
      }
    }
    stage ('DEV Deploy') {
      steps {
      echo 'deploying to DEV Env'
      deploy adapters: [tomcat9(credentialsId: '899760b0-ebb6-4ba8-9d0a-bf4b5ca9119b', path: '', url: 'http://100.24.238.126:8080/')], contextPath: null, war: '**/*.war'
      }
    }
    stage ('Slack Notification') {
      steps {
       echo "deployed to DEV Env successfully"
        slackSend(channel:'devops201', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }
  }
}
