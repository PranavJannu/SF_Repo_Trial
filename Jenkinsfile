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
        // Use shell commands to read file
        //bat 'type manifest/package.xml'
        //powershell 'Get-Content manifest/package.xml'
        //bat """
        //    cat manifest/package.xml
        //"""
        def packageXmlContent = readFile 'manifest/package.xml'
        echo "Package.xml content: ${packageXmlContent.trim()}" //Disply files present under package.xml
        echo "Reading package.xml file"

        // Get the list of files in the classes, lwc, and aura directories
        def classesDir = 'force-app/main/default/classes'
        def lwcDir = 'force-app/main/default/lwc'
        def auraDir = 'force-app/main/default/aura'

        def classesFiles = findFiles(glob: "${classesDir}/**/*.cls").collect { it.path }
        def lwcFiles = findFiles(glob: "${lwcDir}/**/*.js").collect { it.path }
        def auraFiles = findFiles(glob: "${auraDir}/**/*.cmp").collect { it.path }

        // Check if each file listed in package.xml exists in the corresponding directory
        packageXmlContent.eachLine { line ->
        def fileName = line.tokenize('<')[0].trim() // Extract filename from XML entry

        if (fileName.endsWith('.cls') && !(fileName in classesFiles)) {
        error "Class file ${fileName} listed in package.xml does not exist"
        } else if (fileName.endsWith('.js') && !(fileName in lwcFiles)) {
        error "LWC file ${fileName} listed in package.xml does not exist"
        } else if (fileName.endsWith('.cmp') && !(fileName in auraFiles)) {
        error "Aura component file ${fileName} listed in package.xml does not exist"
    }
}
echo "Package.xml validation completed successfully"
    }
}
