import groovy.json.JsonSlurperClassic

pipeline {
    agent any
    parameters {
        string(name: 'REPO_URL', defaultValue: 'https://github.com/PranavJannu/SF_Repo_Trial
', description: 'GitHub repository URL')
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
                    script {
                        if (isUnix()) {
                            sh "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                            sh "${toolbelt} force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
                        } else {
                            bat "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                            bat "\"${toolbelt}\" force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
                        }
                    }
                }
            }
        }

        // Add other stages as needed
    }
}
