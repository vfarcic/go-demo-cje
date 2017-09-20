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
      steps {
        sh "docker image build -f Dockerfile.test -t vfarcic/go-demo-cje-test:${env.BRANCH_NAME}-${env.BUILD_NUMBER} ."
        withCredentials([usernamePassword(
          credentialsId: "docker",
          usernameVariable: "USER",
          passwordVariable: "PASS"
        )]) {
          sh "docker login -u '$USER' -p '$PASS'"
        }
        sh "docker image push vfarcic/go-demo-cje-test:${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
      }
    }
  }
}