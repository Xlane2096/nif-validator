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
      stage("'Setup") {
          steps{
              sh 'printenv'
          }
      }
  }
}