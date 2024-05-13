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
        checkout scm
        echo "Under checkout source stage"
        bat 'git ls-files'
    }

    stage('Read Files') {
        def packageXmlContent = readFile 'manifest/package.xml'
        echo "Package.xml content: ${packageXmlContent.trim()}"
        echo "Reading package.xml file"

        def classesDir = 'force-app/main/default/classes'
        def classesFiles = findFiles(glob: "${classesDir}/**/*.cls").collect { it.path }

        echo "Files in ${classesDir}:"
        classesFiles.each { fileName ->
            echo fileName
        }

        // Validate class names
        def packageClasses = packageXmlContent.readLines().findAll { line ->
            line.contains('<members>') && line.contains('</members>') && !line.contains('<members>*</members>')
        }.collect { line ->
            line.replaceAll(/<members>|<\/members>/, '').trim()
        }

        def existingClasses = classesFiles.collect { it.replaceAll(/^.*\//, '').replaceAll(/\.cls$/, '') }

        def missingClasses = packageClasses.findAll { className ->
            !(className in existingClasses)
        }

        if (missingClasses) {
            echo "Missing classes in package.xml:"
            missingClasses.each { echo it }
            error "Validation failed: Some classes are missing in package.xml"
        } else {
            echo "All classes are present in package.xml"
        }
    }
}
