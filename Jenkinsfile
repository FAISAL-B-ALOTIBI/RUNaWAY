pipeline {

	agent any

	environment {
		DOCKERHUB_CREDENTIALS_PSW = credentials('faisal-dockerhub-token')
        DOCKERHUB_CREDENTIALS_USR = 'faisalalotibi'
		AWS_ACCESS_KEY_ID     = credentials('faisal-aws-secret-key-id')
  		AWS_SECRET_ACCESS_KEY = credentials('faisal-aws-secret-access-key')
		ARTIFACT_NAME = 'Dockerrun.aws.json'
		AWS_S3_BUCKET = 'faisal-belt2-artifacts-123456'
		AWS_EB_APP_NAME = 'faisal-Belt2-eb'
        AWS_EB_ENVIRONMENT_NAME = 'Faisalbelt2eb-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_REGION = 'me-south-1'
	}

	stages {

		stage('Build') {
			steps {
				sh 'docker build -t faisalalotibi/runaway:latest .'
			}
		}

		stage('Login') {
			steps {
				sh 'docker login -u=$DOCKERHUB_CREDENTIALS_USR -p=$DOCKERHUB_CREDENTIALS_PSW'
			}
		}

		stage('Push') {
			steps {
				sh 'docker push faisalalotibi/runaway:latest'
			}
		}

        stage('Deploy') {
            steps {
                sh 'aws configure set region $AWS_REGION'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT_NAME --version-label $AWS_EB_APP_VERSION'
            }
	}
    }
	post {
		always {
			sh 'docker logout'
		}
	}
}