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
          steps{
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
        steps{
          sh"""
          pip install --user -r requirements.txt
          pip install --user -r requirements-test.txt
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
        steps{
          sh 'python3 -m pytest --junitxml results.xml tests/'
        }
        post {
          always {
            archiveArtifacts artifacts: 'results.xml', fingerprint: true
            junit 'results.xml'
          }
        }
      } 
      stage('Coverage report'){
        agent {
          docker {
            image 'python:3.11-slim'
            reuseNode true
          }
        }
        steps {
          sh"""
          python3 -m coverage run --source=. --omit=tests/* -m pytest tests
          python3 -m coverage report -m
          python3 -m coverage html
          """
        }
        post {
          always {
            publishHTML(target: [
              reportDIR: 'htmlcov'
              reportFiles: 'index.html'
              reportName: 'Coverage report'
            ])
          }
        }
      }
  }
}