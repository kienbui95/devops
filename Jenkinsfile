pipeline {
  agent any
  stages {
    stage('Prepare') {
      steps {
        git(url: 'git@github.com:kienbui95/devops.git', branch: 'develop')
      }
    }
    stage('Build') {
      steps {
        isUnix()
        sh '''"\'${mvnHome}/bin/mvn\' -Dmaven.test.failure.ignore clean package"
'''
      }
    }
    stage('Unit Test') {
      parallel {
        stage('Unit Test') {
          steps {
            junit 'target/surefire-reports/TEST-*.xml'
            archiveArtifacts 'target/*.jar'
          }
        }
        stage('Integration Test') {
          steps {
            isUnix()
            sh '"\'${mvnHome}/bin/mvn\' -Dmaven.test.failure.ignore clean verify"'
          }
        }
      }
    }
    stage('Artifacts') {
      parallel {
        stage('Artifacts') {
          steps {
            isUnix()
            sh '"\'${mvnHome}/bin/mvn\' -Dmaven.test.failure.ignore clean deploy"'
          }
        }
        stage('Release') {
          steps {
            echo 'test'
          }
        }
      }
    }
    stage('Deploy') {
      steps {
        echo 'deploy'
      }
    }
  }
}