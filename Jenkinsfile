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
        echo "Under checkout source stage"
        bat 'git ls-files'
    }

    stage('Read Files') {
        def packageXmlContent = readFile 'manifest/package.xml'
        echo "Package.xml content: ${packageXmlContent.trim()}" //Disply files present under package.xml
        echo "Reading package.xml file"

        // Get the list of files in the classes directory
        def classesDir = 'force-app/main/default/classes'
        def classesFiles = findFiles(glob: "${classesDir}/**/*.cls").collect { it.path }

        // Output the files present in the classes directory
        echo "Files in ${classesDir}:"
        classesFiles.each { fileName ->
            echo fileName
        }

        // Check if each class listed in package.xml exists in the classes directory
        packageXmlContent.eachLine { line ->
        def fileName = line.tokenize('<')[0].trim() // Extract filename from XML entry
        if (fileName.endsWith('.cls') && !(fileName in classesFiles)) {
            error "Class file ${fileName} listed in package.xml does not exist in the classes directory"
            }
        }
    }
}
