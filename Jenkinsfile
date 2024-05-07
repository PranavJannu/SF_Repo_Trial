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
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Validate Package.xml') {
            // Read the package.xml file
            def packageXmlContent = readFile('manifest/package.xml')
            // Parse package.xml to extract metadata components
            def metadataTypes = packageXmlContent.readLines().findAll { it.contains('<name>') }.collect { it.replace('<name>', '').replace('</name>', '').trim() }
            // Check if each metadata component exists in the source directory
            def missingMetadata = metadataTypes.findAll { metadataType ->
                def metadataFiles
                if (isUnix()) {
                    metadataFiles = sh(script: "ls force-app/main/default/*.${metadataType} 2> /dev/null", returnStdout: true).trim().split('\n')
                } else {
                    metadataFiles = bat(script: "dir /B force-app/main/default\\*.${metadataType}", returnStdout: true).trim().split('\n')
                }
                metadataFiles.empty
            }
            // If any missing metadata components are found, fail the build
            if (missingMetadata) {
                error "Validation Failed: The following metadata components are missing in force-app/main/default: ${missingMetadata.join(', ')}"
            } else {
                echo "Validation Successful: All metadata components listed in package.xml are present in force-app/main/default"
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
