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
        sh "docker-compose run --rm unit"
      }
    }
  }
}