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
        stage('Deploy') {
            steps {
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
            }
        }
    }
    post {
        always {
            echo 'Stage is success'
        }
    }
}
