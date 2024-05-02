pipeline {
    agent any
    
    stages {
        stage('Check Package.xml File') {
            steps {
                script {
                    //Get the modification time of package.xml file
                    def packageXmlModTime = file('manifest/package.xml').lastModified()

                    //Loop through all files in default directory
                    def defaultDirectory = files('force-app/main/default')
                    def files = defaultDirectory.listFiles()
                    def componentModified = false

                    for (file in files) {
                        //check if the file is not a directory and its modification time is after the package.xml
                        if (!file.isDirectory() && file.lastModified() > packageXmlModTime) {
                            echo "${file.name} is updated."
                            componentModified = true
                        }
                    }

                    //If no component are found to be updated
                    if (!componentModified) {
                        error 'No component is updated. Please update the package.xml file or other component.'
                    }
                }
            }
        }

        stage('Deploy Code') {
            steps {
                script {
                    def BUILD_NUMBER = env.BUILD_NUMBER
                    def RUN_ARTIFACT_DIR = "tests/${BUILD_NUMBER}"
                    def SFDC_USERNAME

                    def HUB_ORG = env.HUB_ORG_DH
                    def SFDC_HOST = env.SFDC_HOST_DH
                    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
                    def CONNECTED_APP_CONSUMER_KEY = env.CONNECTED_APP_CONSUMER_KEY_DH

                    def toolbelt = tool 'toolbelt'

                    // Checkout source
                    checkout scm

                    // Authenticate and grant access
                    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
                        if (isUnix()) {
                            sh "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                        } else {
                            bat "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                        }
                    }

                    // Deployment Command
                    if (isUnix()) {
                        sh "${toolbelt} force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
                    } else {
                        bat "\"${toolbelt}\" force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
                    }
                }
            }
        }
    }
}
