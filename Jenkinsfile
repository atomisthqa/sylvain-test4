import groovy.json.JsonOutput

/*
 * Retrieve current SCM information from local checkout
 */
def getSCMInformation() {
    def gitRemoteUrl = sh(returnStdout: true, script: 'git config --get remote.origin.url').trim()
    def gitCommitSha = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    def gitBranchName = sh(returnStdout: true, script: 'git name-rev --always --name-only HEAD').trim().replace('remotes/origin/', '')

    return [
        url: gitRemoteUrl,
        branch: gitBranchName,
        commit: gitCommitSha
    ]
}

/*
 * Notify the Atomist services about the status of a build based from a
 * git repository.
 */
def notifyAtomist(buildStatus, buildPhase="FINALIZED",
                  endpoint="https://webhook-staging.atomist.services/atomist/jenkins") {

    def payload = JsonOutput.toJson([
        name: env.JOB_NAME,
        duration: currentBuild.duration,
        build      : [
            number: env.BUILD_NUMBER,
            phase: buildPhase,
            status: buildStatus,
            full_url: env.BUILD_URL,
            scm: getSCMInformation()
        ]
    ])

    sh "curl --silent -XPOST -H 'Content-Type: application/json' -d '${payload}' ${endpoint}"
}
node {
    try {
        stage('Checkout') {
            checkout scm
            notifyAtomist("STARTED", "STARTED")
            echo "cloned"
        }
        stage('Build') {
            echo "let's get started"
            sleep 5
            echo "almost"
            sleep 3
            echo "finished"
        }
        notifyAtomist("SUCCESS")
    } catch(e) {
        notifyAtomist("FAILURE")
        throw e
    }
}
