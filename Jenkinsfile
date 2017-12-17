pipeline {
  agent none

	environment {
		MAJOR_VERSION = 1
			} 


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
			sh "if ![ -d'/var/www/html/${env.BRANCH_NAME}' ];  then mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}; fi "
			sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
			}
		}
		stage('Running on CentOS') {
  			agent {
    				label 'CentOS'
				  }
  			steps {
    				sh "wget http://shashank3331.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
    				sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
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
    			sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
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
				sh "git pull origin"
				sh "git checkout master"
				echo "merging development into master"
				sh "git merge Development"
				echo "pushing to origin master"
				sh "git push origin master"
				echo "taaging the release"
				sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
				sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
				}
			}
		}


		post {
                        success{
                                emailext(
                                        subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Development Promoted To Master",
                                        body: """<p> '${env.JOB_NAME} [${env.BUILD_NUMBER}]'Development Promoted To Master" :</p>
                                        <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}] </a>&QUOT;</p>""",
                                        to: "shashank.ujn13@gmail.com"
                                        )
                                }
                        }
		}
		  
		
		post {
			failure{
				emailext(
					subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Failed!",
					body: """<p> '${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed!" :</p>
					<p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}] </a>&QUOT;</p>""",
					to: "shashank.ujn13@gmail.com"
					)
				}
			}
  


