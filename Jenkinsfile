pipeline {
  agent any
  stages {
    stage('Parallel execution') {
      parallel {
        stage('di hola') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('build app') {
          agent {
            docker {
              image 'gradle:6-jdk11'
            }

          }
          steps {
            sh 'ci/build-app.sh'
          }
        }

      }
    }

    stage('Archiva binario') {
      steps {
        archiveArtifacts 'app/build/libs/'
      }
    }

  }
}