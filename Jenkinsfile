pipeline {
    agent any
    parameters {
        string(name: 'REPO_URL', defaultValue: 'https://github.com/PranavJannu/SF_Repo_Trial', description: 'GitHub repository URL')
    }
    stages {
        stage('Clone Repository') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/Dev-1']], userRemoteConfigs: [[url: params.REPO_URL]]])
            }
        }

        stage('Check Salesforce Metadata') {
            steps {
                echo "Checking Salesforce metadata from the GitHub repository"
            }
        }

        stage('Deploy Code') {
            steps {
                withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
                    if (isUnix()) {
                        sh "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                        sh "${toolbelt} force:source:deploy --manifest manifest/package.xml"
                    } else {
                        bat "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                        bat "\"${toolbelt}\" force:source:deploy --manifest manifest/package.xml"
                    }
                }
            }
        }

        // Add other stages as needed
    }
}
