pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                // Checkout your repository (similar to your existing stage)
                checkout scm
            }
        }

        stage('Validate Package.xml') {
            steps {
                script {
                    // Read the package.xml content
                    def packageXmlContent = readFile('C:/ProgramData/Jenkins/.jenkins/workspace/Ckeck Validation/manifest/package.xml')

                    // Extract metadata types from package.xml
                    def metadataTypes = packageXmlContent.readLines().findAll { it.contains('<name>') }
                        .collect { it.replace('<name>', '').replace('</name>', '').trim() }

                    // Validate each metadata type
                    def missingMetadata = []
                    metadataTypes.each { metadataType ->
                        def files = bat(script: "dir /B force-app\\main\\default\\*.${metadataType}", returnStdout: true)
                            .trim().split('\r\n')
                        if (files.isEmpty()) {
                            missingMetadata.add(metadataType)
                        }
                    }

                    // Fail the build if any metadata components are missing
                    if (missingMetadata) {
                        error "Validation Failed: Missing metadata components: ${missingMetadata.join(', ')}"
                    } else {
                        echo "Validation Successful: All metadata components listed in package.xml exist"
                    }
                }
            }
        }

        // Add other stages as needed (e.g., deployment)
    }
}
