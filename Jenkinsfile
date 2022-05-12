pipeline {
    agent any
    environment {
        artifactArtifactId = "java-testing"
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/tushar8871/java-gradle-starter-project.git'
                }
        }
        stage('verifying tools') {
            steps {
                sh ''' #! /bin/bash
                java --version
                gradle -V
                '''
            }
        }
        stage('Build') {
            steps {
                sh ''' #! /bin/bash
                #Including test cases 
                #gradle clean build
                
                #Ignoring test cases 
                gradle clean build #--exclude-task test -i
                '''
            }
        }
        # stage ('Test') {
        #     steps {
        #         sh ''' #! /bin/bash
        #         gradle test
        #         #above command will create a coverage folder which will be used by sonarScanner plugin needed jacoco
        #         #CI=true (only if needed, if the test are stuck)
        #         '''
        #     }	
        #     post {
        #         success {
        #             junit 'target/surefire-reports/**/*.xml' 
        #         }
        #     }
        # }
        # stage('Sonarqube') {
        #     environment {
        #         scannerHome = tool 'sonarScanner'
        #         }
        #     steps {
        #         withSonarQubeEnv('sonar') {
        #             sh "${scannerHome}/bin/sonar-scanner"
        #         }
        #         timeout(time: 5, unit: 'MINUTES') {
        #             waitForQualityGate abortPipeline: true
        #         }
        #     }
        #  }
        #  stage ('Artifact') {
        #     steps {
        #         sh ''' #! /bin/bash
        #         cd /var/lib/jenkins/workspace/
		# 		zip -r order_service.zip ${project_directory}/
				
		# 		#To push code to particular server
		# 		#scp -r ${zip_name}.zip ubuntu@${Private_IP}:/home/ubuntu/
				
		# 		#To push zip folder to s3 
        #         aws s3 cp order_service.zip  s3://${Bucket_name}/
        #         '''
        #     }
        # }
        stage('Deploy') {
            withAwsCli(credentialsId: 'Cloud-coe-aws', defaultRegion: 'us-east-1') {

                // DEPLOY TO BEANSTALK
                def destinationJarFile = "${env.artifactArtifactId}-${env.BUILD_NUMBER}.jar"
                def versionLabel = "${env.artifactArtifactId}#${env.BUILD_NUMBER}"
                def description = "${env.BUILD_URL}"
                sh """\
                    aws s3 cp $artifactArtifactId-*.jar s3://beesbank/$destinationJarFile
                    aws elasticbeanstalk create-application-version --source-bundle S3Bucket=java-test-buc,S3Key=$destinationJarFile --application-name ${env.artifactArtifactId} --version-label $versionLabel --description \\\"$description\\\"
                    aws elasticbeanstalk update-environment --environment-name Javatesting-env --application-name ${env.artifactArtifactId} --version-label $versionLabel --description \\\"$description\\\"
                """
            }
            # steps {
            #     sh ''' #! /bin/bash
				
			# 	#to deploy on particular server
            #     ssh -i /var/lib/jenkins/.ssh/id_rsa ubuntu@${private_IP} '
			# 	for pid in $(lsof -t -i:8000); do                        kill -9 $pid;                done
			# 	unzip file.zip -d destination_folder
			# 	cd /home/ubuntu/${project_name}/target/
            #     nohup java -jar ${jar_name.jar}  > /dev/null 2>&1 &
            #     '
				
			# 	#to deploy on aws from s3 bucket
            #     #aws deploy create-deployment --application-name ${Application_name} --deployment-group-name ${deployment-group-name} --deployment-config-name CodeDeployDefault.AllAtOnce --s3-location bucket=${bucket_name},bundleType=${type_of_artifact like zip,gz,tgz},key=${filename.zip/gz/tgz}    
            #     #echo Deployment Successfull
            #     '''
            # }
        }
    }
    post {
        always {
            echo 'Stage is success'
        }
    }
}
