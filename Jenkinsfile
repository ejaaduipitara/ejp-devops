node() {
    try {
        String ANSI_GREEN = "\u001B[32m"
        String ANSI_NORMAL = "\u001B[0m"
        String ANSI_BOLD = "\u001B[1m"
        String ANSI_RED = "\u001B[31m"
        String ANSI_YELLOW = "\u001B[33m"

        ansiColor('xterm') {
            stage('Checkout') {
                if (!env.hub_org) {
                    println(ANSI_BOLD + ANSI_RED + "Uh Oh! Please set a Jenkins environment variable named hub_org with value as registery/sunbirded" + ANSI_NORMAL)
                    error 'Please resolve the errors and rerun..'
                } else {
                    println(ANSI_BOLD + ANSI_GREEN + "Found environment variable named hub_org with value as: " + env.hub_org + ANSI_NORMAL)
                }
            }
            cleanWs()

            checkout scm
            commit_hash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
            build_tag = sh(script: "echo " + params.github_release_tag.split('/')[-1] + "_" + commit_hash + "_" + env.BUILD_NUMBER, returnStdout: true).trim()
            echo "build_tag: " + build_tag

            sh 'chmod +x ./build.sh'

            stage('Build') {
                sh("./build.sh ${build_tag} ${env.NODE_NAME} ${env.hub_org}")
            }

            stage('ArchiveArtifacts') {
                archiveArtifacts artifacts: "metadata.json"
                currentBuild.description = "${build_tag}"
            }
        }
    } catch (err) {
        currentBuild.result = "FAILURE"
        throw err
    }
}
