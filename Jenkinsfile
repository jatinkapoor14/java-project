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
	agent {label 'master openjdk:8u131-jre'}
     steps {
	   sh 'docker run -itd --name jatindock openjdk:8u131-jre /bin/bash'
	   sh 'docker exce -it jatindock /bin/bash whoami'
      }
    }
  }
}
