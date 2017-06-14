pipeline {
  agent none

  stages {
    stage('Unit Tests') {
	agent {label 'master'}
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build') {
	agent {label 'master'}
      steps {
        sh 'ant -f build.xml -v'
      }
	  post {
        always {
           archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
		}
    }
    stage('deploy') {
	agent {label 'master'}
     steps {
       sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
    }
    stage('Funtional testing') {
	agent {docker 'docker.io/openjdk:8u121-jre'}
     steps {
	   sh 'echo whoami'
      }
    }
  }
}
