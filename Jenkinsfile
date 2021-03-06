import java.text.SimpleDateFormat

pipeline {
  agent {
    label "docker-compose"
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
        sh "docker-compose version"
      }
    }
    stage("test-go") {
      agent {
        label "go"
      }
      steps {
        sh "go get -d -v -t && go test --cover -v ./... --run UnitTest && go build -v -o go-demo"
      }
    }
    stage("test-docker") {
      steps {
        sh "docker container run -v ${workspace}:/usr/src/myapp -w /usr/src/myapp golang:1.9 bash -c \"go get -d -v -t && go test --cover -v ./... --run UnitTest && go build -v -o go-demo\""
      }
    }
    stage("test-dc") {
      steps {
        sh "docker-compose run --rm unit"
      }
    }
    stage("release") {
      when {
        branch "master"
      }
      steps {
        script {
          def dateFormat = new SimpleDateFormat("yy.MM.dd")
          currentBuild.displayName = dateFormat.format(new Date()) + "-" + env.BUILD_NUMBER
        }
        sh "docker image build -t vfarcic/go-demo-cje ."
        sh "docker image tag vfarcic/go-demo-cje vfarcic/go-demo-cje:${currentBuild.displayName}"
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