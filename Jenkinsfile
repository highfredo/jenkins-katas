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
        stash(excludes: '.git', name: 'code')
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
          agent {
            docker {
              image 'gradle:6-jdk11'
            }

          }
          options {
            skipDefaultCheckout(true)
          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash 'code'
          }
        }

        stage('test app') {
          agent {
            docker {
              image 'gradle:6-jdk11'
            }

          }
          options {
            skipDefaultCheckout(true)
          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

    stage('build docker') {
      steps {
        unstash 'code'
        sh 'ci/build-docker.sh'
        stash 'code'
      }
    }

    stage('push docker') {
      environment {
        DOCKERCREDS = credentials('docker_login')
      }
      when { 
        beforeAgent true
        branch "master" 
      }
      steps {
        unstash 'code'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
        sh 'ci/push-docker.sh'
      }
    }

    stage('component test') {
      agent {
        docker {
          image 'gradle:6-jdk11'
        }
      }
      when { 
        beforeAgent true
        branch pattern: "dev/*"
      }
      options {
        beforeAgent true
        skipDefaultCheckout(true)
      }
      steps {
        unstash 'code'
        sh 'ci/component-test.sh'
      }
    }

  }
  environment {
    docker_username = 'highfredo'
  }
  post {
    cleanup {
      deleteDir()
    }

  }
}
