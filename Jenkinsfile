pipeline {
  agent none
  
  environment {
    MAJOR_VERSION = 1
  }

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
       sh "mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}"
	   sh "cp dist/rectangle_${env.MAJOR_VERSION}_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
    }
    stage('Funtional testing') {
	agent {label 'master'}
     steps {
	   sh "docker run -itd --name jatindock_${env.BRANCH_NAME}_${env.BUILD_NUMBER} openjdk:8u131-jre /bin/bash"
	   sh "docker exec -i jatindock_${env.BRANCH_NAME}_${env.BUILD_NUMBER} wget http://192.168.1.108/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}_${env.BUILD_NUMBER}.jar"
	   sh "docker exec -i jatindock_${env.BRANCH_NAME}_${env.BUILD_NUMBER} java -jar rectangle_${env.MAJOR_VERSION}_${env.BUILD_NUMBER}.jar 3 4"
	   sh "docker stop jatindock_${env.BRANCH_NAME}_${env.BUILD_NUMBER}"
	   sh "docker rm jatindock_${env.BRANCH_NAME}_${env.BUILD_NUMBER}"
      }
    }
	stage('Promote to green') {
	agent {label 'master'}
	when {branch 'master'}
     steps {
	   sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/" 
      }
    }
	stage('Promote Dev to master') {
	agent {label 'master'}
	when {branch 'development'}
	  steps {
	  echo "Stashing"
	  sh "git stash"
	  echo "Checking out development brach"
	  sh "git checkout development"
	  echo 'Checking Out Master Branch'
      sh 'git pull origin'
      sh 'git checkout master'
	  echo "Merging development in to master brach"
	  sh "git merge development"
	  echo "Pushing to origin master"
	  sh "git push origin master"
	  echo "Tagging the release"
	  sh "git tag rectangle-${env.MAJOR_VERSION}-${env.BUILD_NUMBER}"
	  sh "git push origin rectangle-${env.MAJOR_VERSION}-${env.BUILD_NUMBER}"
	  echo "All Done!"
	  }
	    post {
          failure{
	         emailtext(
		       subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Failed!",
               body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed!":</p>
               <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
               to: "jatinkapoor14@gmail.com"
		)
		}
        }
	}
  }
  post {
     failure{
	    emailtext(
		  subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Failed!",
          body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed!":</p>
          <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
          to: "jatinkapoor14@gmail.com"
		)
		}
  }
}
