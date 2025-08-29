pipeline {
	agent {
        label 'AGENT-1'
    }
    environment {
        appVersion = ''
    }
    options {
        timeout(time: 10, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }
    /* parameters {
        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')

        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')

        booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')

        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    } */
	stages {
        stage('Read package.json') {
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
        }
		stage('Build') {
			steps {
				script {
                    sh """
                        echo 'Building'
                        sleep 10
                        env
                    """
                }
			}
		}
		stage('Test') {
            input {
                message "Should we continue?"
                ok "Yes, we should."
                submitter "alice,bob"
                parameters {
                    string(name: 'PERSON', defaultValue: 'M Charan', description: 'Who should I say hello to?')
                }
            }
			steps {
				script {
                    echo "Hello ${PERSON}, nice to meet you."
                }
			}
		}
		stage('Deploy') {
			steps {
				script {
                    sh 'echo Deploying'
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