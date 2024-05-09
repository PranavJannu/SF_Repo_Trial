import groovy.json.JsonSlurperClassic

node {
    def BUILD_NUMBER = env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR = "tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG = env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY = env.CONNECTED_APP_CONSUMER_KEY_DH

    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // When running in a multi-branch job, issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploy Code') {
            if (isUnix()) {
                sh "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            } else {
                bat "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }

            // Create a manifest dynamically
            if (isUnix()) {
                sh "${toolbelt} force:source:manifest:create --sourcepath force-app --manifestname manifest/deployPackage"
            } else {
                bat "\"${toolbelt}\" force:source:manifest:create --sourcepath force-app --manifestname manifest/deployPackage"
            }

            // Validate the deployment
            def validationExitCode
            if (isUnix()) {
                validationExitCode = sh script: "${toolbelt} force:source:deploy --manifest manifest/deployPackage.xml --predestructivechanges manifest/destructiveChangesPre.xml --postdestructivechanges manifest/destructiveChangesPost.xml", returnStatus: true
            } else {
                validationExitCode = bat script: "\"${toolbelt}\" force:source:deploy --manifest manifest/deployPackage.xml --predestructivechanges manifest/destructiveChangesPre.xml --postdestructivechanges manifest/destructiveChangesPost.xml", returnStatus: true
            }

            if (validationExitCode == 0) {
                // Validation succeeded, proceed with deployment
                if (isUnix()) {
                    sh "${toolbelt} force:source:deploy --manifest manifest/deployPackage.xml"
                } else {
                    bat "\"${toolbelt}\" force:source:deploy --manifest manifest/deployPackage.xml"
                }
            } else {
                // Validation failed, do not proceed with deployment
                error "Validation failed. Deployment aborted."
            }
        }
    }
}
