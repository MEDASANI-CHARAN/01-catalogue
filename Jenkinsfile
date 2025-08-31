pipeline {
	agent {
        label 'AGENT-1'
    }
    environment {
        appVersion = ''
        REGION = 'us-east-1'
        ACC_ID = '009160060207'
        PROJECT = 'roboshop'
        COMPONENT = 'catalogue'
    }
    options {
        timeout(time: 10, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }
    parameters {
        // string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')

        // text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')

        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')

        // choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

        // password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    }
	stages {
        stage('Read package.json'){
            steps {
                script {
                    // Read the package.json file
                    // def means define 
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                    echo "Project Version: ${appVersion}"
                    }
                }
            }
        stage('Install Dependencies'){
            steps {
                script {
                    sh"""
                        npm install
                    """
                    }
                }
            }
        stage('Unit Testing'){
            steps {
                script {
                    sh"""
                        echo "unit tests"
                    """
                    }
                }
            }
        
		stage('Docker Build'){
			steps {
				script {
                        withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                            sh """
                                aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                                docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                                docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                            """
                        }
                }
			}
		}
        stage('Trigger deploy'){
            when {
                    expression { params.deploy }
                }
			steps {
				script {
                    build job: 'catalogue-cd', // Replace 'DownstreamJobName' with the actual name of your downstream job
                    parameters: [
                        string(name: 'appVersion', value: "${appVersion}"),
                        string(name: 'deploy_to', value: "dev")
                        ],
                    propagate: true,
                    wait: true 
                }
			}
		}
    }
	
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'I will always say success!'
        }
        failure { 
            echo 'I will always say failure!'
        }
    }
}