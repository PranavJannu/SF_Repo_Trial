import groovy.json.JsonSlurperClassic

pipeline {
    agent any
    parameters {
        string(name: 'REPO_URL', defaultValue: 'https://github.com/PranavJannu/SF_Repo_Trial', description: 'GitHub repository URL')
    }

    stage('Check Salesforce Metadata') {
            steps {
                echo "Checking Salesforce metadata from the GitHub repository"
            }
        }
    
    stages {
        stage('Clone Repository') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/Dev-1']], userRemoteConfigs: [[url: params.REPO_URL]]])
            }
        }

    stage('Validate Package.xml') {
        steps {
            echo "Checking files in: force-app/main/default" // Add this line
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
                        metadataFiles = bat(script: "dir /B force-app\\main\\default\\*.${metadataType}", returnStdout: true).trim().split('\r\n')
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
        }


        /*stage('Deploy Code') {
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
        }*/

        // Add other stages as needed
    }
}
