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
    stage("versions") {
      steps {
        sh "java -version"
        sh "docker version"
      }
    }
    stage("test") {
      steps {
        sh "docker container run -it -v $PWD:/usr/src/myapp -w /usr/src/myapp golang:1.9 bash -c \"pwd && go get -d -v -t && go test --cover -v ./... --run UnitTest && go build -v -o go-demo\""
      }
    }
    stage("release") {
      steps {
        script {
          def dateFormat = new SimpleDateFormat("yy.MM.dd")
          currentBuild.displayName = dateFormat.format(new Date()) + "-" + env.BUILD_NUMBER
        }
        sh "docker image build -t vfarcic/go-demo-cje"
        sh "docker image tag -t vfarcic/go-demo-cje vfarcic/go-demo-cje:${currentBuild.displayName}"
        withCredentials([usernamePassword(
          credentialsId: "docker",
          usernameVariable: "USER",
          passwordVariable: "PASS"
        )]) {
          sh "docker login -u '$USER' -p '$PASS'"
        }
        sh "docker image push vfarcic/go-demo-cje"
        sh "docker image push vfarcic/go-demo-cje:${currentBuild.displayName}"
      }
    }
  }
}