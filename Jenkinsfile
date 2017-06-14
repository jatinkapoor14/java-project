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
        success {
           archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
		}
    }
    stage('deploy') {
	agent {label 'master'}
	 steps {
       sh "mkdir /var/www/html/rectangles/all/$(env.BRANCH_NAME)"
	   sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/$(env.BRANCH_NAME)"
      }
    }
    stage('Funtional testing') {
	agent {label 'master'}
     steps {
	   sh 'docker run -itd --name jatindock openjdk:8u131-jre /bin/bash'
	   sh "docker exec -i jatindock  wget http://192.168.1.108/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
	   sh " docker exec -i jatindock java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
	   sh 'docker stop jatindock'
	   sh 'docker rm jatindock'
      }
    }
	stage ('Promote Dev to master') {
	agent {label 'master'}
	when {branch 'development'}
	  steps {
	  echo "Stashing"
	  sh "git stash"
	  echo "Checking out development brach"
	  sh "git checkout development"
	  echo "Merging development in to master brach"
	  sh "git merge development"
	  echo "Pushing to origin master"
	  sh "git push orgin master"
	  }
	}
  }
}
