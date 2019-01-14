pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'gradle build'
        bat 'gradle javadoc'
        bat 'gradle jar'
        archiveArtifacts 'build/libs/*.jar'
        archiveArtifacts 'build/docs/javadoc/'
      }
    }
    stage('Mail Notification') {
      steps {
        mail(to: 'fm_bouayache@esi.dz', subject: 'Notification Build', body: 'Build reussi')
      }
    }
   
    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            withSonarQubeEnv('sonarqube') {
              bat 'sonar-scanner'
            }
           // waitForQualityGate true
          }
        }
    stage('Test Reporting') {
          steps {
            jacoco(maximumBranchCoverage: '70')
          }
        }
      }
	  }
      
    
    stage('Deployement') {
     when{
            NOT{
              changeRequest target : 'master'
            }
          }
      steps {
        bat 'gradle uploadArchives'
      }
    }
    stage('Slack Notification') {
	    when{
            NOT{
              changeRequest target : 'master'
            }
          }
      steps {
        slackSend(message: '"Testing Jenkins"')
      }
    }
  }
}
