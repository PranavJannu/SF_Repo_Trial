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

        stage('Validate Package.xml') {
            steps {
                script {
                    // Get a list of metadata types from the package.xml
                    def packageXmlContent = readFile('C:/ProgramData/Jenkins/.jenkins/workspace/Ckeck Validation/manifest/package.xml')
                    def metadataTypes = packageXmlContent.readLines().findAll { it.contains('<name>') }.collect { it.replace('<name>', '').replace('</name>', '').trim() }
                    
                    // Check for each metadata type if corresponding files exist in force-app/main/default directory
                    def missingMetadata = []
                    metadataTypes.each { metadataType ->
                        def files = bat(script: "dir /B force-app\\main\\default\\*.${metadataType}", returnStdout: true).trim().split('\r\n')
                        if (files.isEmpty()) {
                            missingMetadata.add(metadataType)
                        }
                    }
                    
                    // If any missing metadata components are found, fail the build
                    if (missingMetadata) {
                        error "Validation Failed: The following metadata components are missing in force-app/main/default: ${missingMetadata.join(', ')}"
                    } else {
                        echo "Validation Successful: All metadata components listed in package.xml are present in force-app/main/default"
                    }
                }
            }
        }

        stage('Check Salesforce Metadata') {
            steps {
                echo "Checking Salesforce metadata from the GitHub repository"
            }
        }

        // Add other stages as needed
    }
}
