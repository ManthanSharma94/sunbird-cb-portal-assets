@Library('deploy-conf') _
node() {
    try {
        String ANSI_GREEN = "\u001B[32m"
        String ANSI_NORMAL = "\u001B[0m"
        String ANSI_BOLD = "\u001B[1m"
        String ANSI_RED = "\u001B[31m"
        String ANSI_YELLOW = "\u001B[33m"

        stage('checkout public repo') {
            folder = new File("$WORKSPACE/.git")
            if (folder.exists())
            {
               println "Found .git folder. Clearing it.."
               sh'git clean -fxd'
            }  
            checkout scm
        }

        stage('deploy') {
            values = docker_params()
            jobName = sh(returnStdout: true, script: "echo $JOB_NAME").split('/')[-1].trim().toLowerCase()
            currentWs = sh(returnStdout: true, script: 'pwd').trim()
            values.put('currentWs', currentWs)
            values.put('ansiblePlaybook', ansiblePlaybook)
            values.put('ansibleExtraArgs', ansibleExtraArgs)
            archiveArtifacts 'metadata.json'
            currentBuild.description = "Image: ${values.image_tag}, Private: ${params.private_branch}, Public: ${params.branch_or_tag}"
        }
         summary()
     }
    catch (err) {
        currentBuild.result = "FAILURE"
        throw err
    }
    finally {
        slack_notify(currentBuild.result)
        email_notify()
    }
}
