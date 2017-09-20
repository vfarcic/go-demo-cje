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
    stage("checkout") {
      steps {
        checkout scm
        stash name: "compose", includes: "docker-compose.yml"
      }
    }
    stage("build") {
      environment {
        BETA_TAG = "beta-${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
      }
      steps {
        sh "docker image build -t vfarcic/go-demo-cje:beta-${env.BETA_TAG} ."
        withCredentials([usernamePassword(
          credentialsId: "docker",
          usernameVariable: "USER",
          passwordVariable: "PASS"
        )]) {
          sh "docker login -u '$USER' -p '$PASS'"
        }
        echo "docker image push vfarcic/go-demo-cje:beta-${env.BETA_TAG}"
      }
    }
  }
}