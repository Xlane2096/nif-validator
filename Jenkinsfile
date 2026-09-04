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
            publishHTML(target:[
              reportDir: 'htmlcov',
              reportFiles: 'index.html',
              reportName: 'Coverage report'
            ])
          }
        }
      }
      stage('Deliver') {
        steps{
          withCredentials([usernamePassword(credentialsId: 'dockerHub',
            usernameVariable: 'username', passwordVariable: 'password')]){
            sh"""
            docker login -u ${username} -p ${password}
            docker build -t ${username}/nif-validator .
            docker push ${username}/nif-validator
            """
          } 
        }
      }
      stage('Deployment') {
        steps {
          sshagent(credentialsId:['redhat']) {
            sh"""
            ssh -o StrictHostKeyChecking=no redhat@3.78.218.63 "docker run -d -p 8080:9046 dfonseca96/nif-validator"
            """
          }
        }
      }
  }
}