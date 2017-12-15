pipeline {
  agent none 


  stages {


	stage('Unit Tests') {
		agent {
  			label 'apache'
			}
		steps {
			sh 'ant -f test.xml -v'
			junit 'reports/result.xml'
			}
		}


	stage('build') {
		agent {
  			label 'apache'
			}
      		steps {
      			sh 'ant -f build.xml -v'
     			 }
			post {
        			success{
                			archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
                			}
        }

    }
	stage('deploy') {
			agent {
  				label 'apache'
				}
		steps {
			sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
			}
		}
		stage('Running on CentOS') {
  			agent {
    				label 'CentOS'
				  }
  			steps {
    				sh "wget https://shashank3331.mylabserver.com:rectanlges/all/rectangle_${env.BUILD_NUMBER}.jar"
    				sh "java -jar rectangle.${env.BUILD_NUMBER}.jar 3 4"
  				}
			}
		  
  }

}
