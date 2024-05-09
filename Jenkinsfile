import groovy.json.JsonSlurperClassic

node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Validate Code') {
            if (isUnix()) {
                sh "${toolbelt} force:source:retrieve -m \"ApexClass, LightningComponentBundle\" -r retrieve -u ${HUB_ORG}"
            } else {
                bat "\"${toolbelt}\" force:source:retrieve -m \"ApexClass, LightningComponentBundle\" -r retrieve -u ${HUB_ORG}"
            }

            // Add logic to validate if components exist in the retrieved package.xml
            // You can parse the retrieved package.xml and compare it with the expected components from your source code
            // If any components are missing, fail the pipeline or handle the error accordingly
            // For example:
            def retrievedPackageXml = readFile('retrieve/package.xml')
            if (!retrievedPackageXml.contains('<ApexClass>') || !retrievedPackageXml.contains('<LightningComponentBundle>')) {
                error "Some components are missing in package.xml"
            }
        }

        stage('Deploy Code') {
            if (isUnix()) {
                sh "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            } else {
                bat "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
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
