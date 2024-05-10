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
    stages{
            stage('checkout source') {
             steps{   
                    // when running in multi-branch job, one must issue this command
                    checkout scm
                    echo "Under checkout source stage"
                }
            }

    stage('Read Files'){
        steps{
            //Use shell commands to read file
            sh 'cat manifiest/package.xml'
            echo "Reading package.xml file"
        }
    }
    }
}

    // Validation Stage
    /*stage('Validation') {
        steps {
            script {
                // Download the changed files list from GitHub
                def changedFiles = sh(script: "curl -sSL https://api.github.com/repos/${env.GIT_BRANCH}/compare/${env.GIT_PREVIOUS_COMMIT}/${env.GIT_COMMIT} | jq -r '.files[].filename'", returnStdout: true).trim()

                // Find all Apex Class, LWC component, and Aura component files in changed_files.txt
                def apexClasses = findFiles(glob: 'force-app/main/default/**/*.cls', from: changedFiles)
                def lwcComponents = findFiles(glob: 'force-app/main/default/lwc/**/*.html', from: changedFiles)
                def auraComponents = findFiles(glob: 'force-app/main/default/aura/**/*.cmp', from: changedFiles)

                // Check if any components were changed
                if (apexClasses.isEmpty() && lwcComponents.isEmpty() && auraComponents.isEmpty()) {
                    error "Validation Failed: No new components found in changed files."
                } else {
                    echo "Found new components:"
                    if (!apexClasses.isEmpty()) {
                        echo "  * Apex Classes:"
                        apexClasses.each { file -> echo "      - $file" }
                    }
                    if (!lwcComponents.isEmpty()) {
                        echo "  * LWC Components:"
                        lwcComponents.each { file -> echo "      - $file" }
                    }
                    if (!auraComponents.isEmpty()) {
                        echo "  * Aura Components:"
                        auraComponents.each { file -> echo "      - $file" }
                    }
                }
            }
        }
    }*/

    /*stage('Deploy Code') {
        withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
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
    }*/

