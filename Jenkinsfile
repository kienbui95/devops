pipeline {
  agent any
  stages {
    stage('Prepare') {
      steps {
        git(url: 'git@github.com:kienbui95/devops.git', branch: 'develop')
        tool 'maven'
      }
    }
    stage('Build') {
      steps {
        isUnix()
        sh '''/root/devops/apache-maven-3.5.4/bin/mvn -Dmaven.test.failure.ignore clean package
'''
      }
    }
    stage('Unit Test') {
      parallel {
        stage('Unit Test') {
          steps {
            junit '**/target/surefire-reports/TEST-*.xml'
            archiveArtifacts(artifacts: 'target/*.jar', allowEmptyArchive: true)
          }
        }
        stage('Integration Test') {
          steps {
            isUnix()
            sh '/root/devops/apache-maven-3.5.4/bin/mvn -Dmaven.test.failure.ignore clean verify'
          }
        }
        stage('Sona') {
          steps {
            sh '/root/devops/apache-maven-3.5.4/bin/mvn sonar:sonar'
          }
        }
      }
    }
    stage('Artifacts') {
      parallel {
        stage('Artifacts') {
          steps {
            isUnix()
            sh '/root/devops/apache-maven-3.5.4/bin/mvn -Dmaven.test.failure.ignore clean deploy'
          }
        }
        stage('Release') {
          steps {
            sh '/root/devops/apache-maven-3.5.4/bin/mvn release:clean release:prepare release:perform'
          }
        }
      }
    }
    stage('HA') {
      steps {
        sh 'curl -u jenkins:jenkins -T target/**.war http://localhost:9080/manager/text/deploy?path=/devops&update=true'
      }
    }
    stage('Deploy') {
      steps {
        sh 'curl -u jenkins:jenkins -T target/**.war http://localhost:8080/manager/text/deploy?path=/devops&update=true'
      }
    }
  }
}