pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID     = credentials('osaid-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('osaid-aws-secret-access-key.')
        ARTIFACT_NAME = 'artifact.jar'
        AWS_S3_BUCKET = 'osaid-belt2d2-artifacts-123456'
        AWS_EB_APP_NAME = 'osaid-Belt2D2-artifacts-123456'
        AWS_EB_ENVIRONMENT = 'Osaidbelt2d2artifacts123456-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
    } 
    stages {

        
        
         stage('Quality Scan') {
            steps {
               
            sh "mvn -Dmaven.test.failure.ignore=true clean compile"

            sh "mvn clean verify sonar:sonar \
  -Dsonar.projectKey=onsite-Osaid-B2D2 \
  -Dsonar.host.url=http://52.23.193.18 \
  -Dsonar.login=sqp_3704c40f15d437ed49d173e30a5ef1f6cd189129"
               
      
               
            }

            
        }
        
        stage('Build') {
            steps {
                // Get some code from a GitHub repository

                // Run Maven on a Unix agent.
                // sh "mvn -Dmaven.test.failure.ignore=true package"
                
                sh "mvn compile"
                
            }
            
             post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
                success {
                    
                    archiveArtifacts 'target/*.jar'
                    sh 'aws s3 cp ./target/**.jar s3://$AWS_S3_BUCKET/$ARTIFACT_NAME'
                    
                }
            }
            
        }
        
       stage('Publish') {
            steps {
                sh 'aws configure set region us-east-1'
             // sh 'aws s3 cp ./target/spring-petclinic-2.3.1.BUILD-SNAPSHOT.jar s3://sda-learning-jenkins/artifact-sda.jar'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            }
            post {
                success {
                    echo 'success Jenkinsfile'
                    // bat 'aws configure set region us-east-1'
                    // bat 'aws s3 cp ./target/calculator-0.0.1-SNAPSHOT.jar s3://sda-learning-jenkins/calculator.jar'
                }
            }
        }
    }
}
