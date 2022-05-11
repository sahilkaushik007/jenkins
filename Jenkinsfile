pipeline {
    agent { label 'master' }
    environment {
        BUILD_USER=getBuildUser()
		GIT=gitCommitURL()  
		BUILD_ENVIRONMENT="Test"
		MODULE_NAME="accounts"
		BUILD_URL="${env.BUILD_URL}"
		REPO=get_repo()
		BU="${BUILD_USER}"
		VERSION=sh (script: "sed -n '/version/p' package.json | cut -b 14- | tr -d \'\",\' ",returnStdout: true).trim()
        	ARTIFACTORY_BINARY="${ARTIFACTORY_URL}/${JFROG_REPO_NAME}/${ENVIRONMENT}/${VERSION}/${MODULE_NAME}/${BUILD_NUMBER}"
		ARTIFACTORY_XRAY="${XRAY_URL}/${JFROG_REPO_NAME}/${ENVIRONMENT}/${VERSION}/${MODULE_NAME}/${BUILD_NUMBER}/manifest.json"
    }
    parameters{
		choice(name: 'ENVIRONMENT', choices: ['dev', 'qa'], description: 'Select the Env Name from the Dropdown List')
		booleanParam(name: 'Deploy', defaultValue: false, description:'Enable it if you want to deploy as well')
		booleanParam(name: 'Sanity_test', defaultValue: false, description:'Enable it if you want to sanity test in testsigma')
		booleanParam(name: 'SonarQube', defaultValue: false, description:'Enable it if you want to run SonarQube scan')
		booleanParam(name: 'Check_Quality_Gate', defaultValue: false, description:'Enable it if you want to run Sonar Quality Gate')
		booleanParam(name: 'Notification', defaultValue: true, description:'Enable it if you want to get build notification')
	}
    
    stages {
        stage('Checkout') {
            steps {
                    checkout_code('cam_server_web',"${REPO}")
					if (env.CHANGE_ID) {
						echo "$CHANGE_AUTHOR"
						BU="$CHANGE_AUTHOR"
					}
					if ("${params.ENVIRONMENT}"=="dev" || "${env.CHANGE_TARGET}"=="developer"){
					   	   ENVIRONMENT="dev"
						   REPOSITORY_NAME = "accounts-dev"
						   CLUSTER = "accounts-dev"
						   FAMILY = "accounts-dev"
						   NAME = "accounts-dev"
						   SERVICE_NAME = "accounts-dev"
						   ECS_TASK_ROLE_ARN = "arn:aws:iam::908878033545:role/Dreamfactory_Task"
						   sh "cp -pr /var/lib/jenkins/jenkins_jobs/accounts-dev/. ."
					}
            }
        }
    }
