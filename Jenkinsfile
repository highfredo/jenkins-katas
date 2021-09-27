pipeline {
  agent any
  stages {
    stage('clone down') {
      agent {
        node {
          label 'master-label'
        }     
      }
      steps {
        stash excludes: '.git', name: 'code'
      }
    }
    stage('Parallel execution') {
      parallel {
        stage('di hola') {
          steps {
            sh 'echo "hello world"'
          }
        }
        stage('build app') {
          options {
            skipDefaultCheckout(true) 
          }
          agent {
            docker {
              image 'gradle:6-jdk11'
            }
          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }
      }
    }
  }
}
