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
			sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
			sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
			}
		}
		stage('Running on CentOS') {
  			agent {
    				label 'CentOS'
				  }
  			steps {
    				sh "wget http://shashank3331.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
    				sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
  				}
			}
		stage('Promote to Green') {
				agent {
					label 'apache'
					}
				when {
					branch 'master'
					}
  			steps{
    			sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
  			}
			}
		stage('Promote Development Branch To Master') {
			agent {
				label 'apache'
				}
			when {
				branch 'Development'
				}
			steps {
				echo "stashing any local changes"
				sh "git stash"
				echo "checking out development branch"
				sh "git checkout Development"
				echo "checking out master branch"
				sh "git checkout master"
				echo "merging development into master"
				sh "git merge Development"
				echo "pushing to origin master"
				sh "git push origin master"
				}
			}
		  
  }

}
