import java.text.SimpleDateFormat

pipeline {
  agent {
    label "docker"
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: "2"))
    disableConcurrentBuilds()
  }
  stages {
    stage("unit-tests") {
      environment {
        TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
      }
      steps {
        sh "TAG=${env.TAG} docker-compose run --rm unit"
        sh "pwd"
        sh "ls -l"
      }
    }
  }
}