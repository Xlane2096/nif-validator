#!groovy
/* NIF-validator
|* 20260904 Daniel Fonseca
|* CI-CD on project
|*/

pipeline {
  agent {
    label 'linux'
    }
  environment {
      HOME = "${env.WORKSPACE}"
  }

  stages {
      stage('Setup') {
          step{
              sh 'printenv'
          }
      }
      stage('Create Docker environment'){
        agent {
          docker {
            image 'python:3.11-slim'
            reuseNode true
          }
        }
        step {
          sh"""
          pip install --user -r requirement.txt
          pip install --user -r requirements-test-txt
          """
        }
      }
      stage ('Unit tests') {
        agent {
          docker {
            image 'python:3.11-slim'
            reuseNode true
          }
        }
        step {
          sh 'python3 -m pytest --junitxml results.xml tests/'
        }
        post {
          always {
            archiveArtifacts artifacts: 'results.xml', fingerprint: true
            junit 'results.xml'
          }
        }
      } 
  }
}