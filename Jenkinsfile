node {
    stage('Prepare') {
        println "Preparing the build..."

        STASH_GIT_REPO="git-repo"
        STASH_BUILD="build-result"

        println "Stashing git repo..."
        dir('../workspace@script'){
            GIT_REF = sh returnStdout: true, script: 'git rev-parse --verify HEAD'
            stash name: STASH_GIT_REPO, includes: '**/*'
        }
        println "Stashed  git repo: 'git-repo'"
        println "Prepared  the build"
    }

    stage('Build') {
        NEXUS_USER="${env.NEXUS_USER}"
        NEXUS_PASSWORD="${env.NEXUS_PASSWORD}"
        NEXUS_MIRROR_URL="${env.MAVEN_MIRROR_URL}"
        MAVEN_REPOSITORY_URL="${env.MAVEN_REPOSITORY_URL}"

        podTemplate(name: 'jenkins-slave-gradle',
                    cloud: 'openshift',
                    containers: [
            containerTemplate(name: 'jnlp',
                            image: 'ci/jenkins-slave-gradle',
                            resourceRequestCpu: '500m',
                            resourceLimitCpu: '4000m',
                            resourceRequestMemory: '1024Mi',
                            resourceLimitMemory: '4096Mi',
                            slaveConnectTimeout: 180
                            )
        ]) {
            node('jenkins-slave-gradle'){
                container('jnlp'){
                    println "Unstashing '${STASH_GIT_REPO}'..."
                    unstash STASH_GIT_REPO
                    println "Unstaheed  '${STASH_GIT_REPO}'"
                    dir('\\complete') {
                        echo sh(returnStdout: true, script: "gradle -PnexusUsername=$NEXUS_USER -PnexusPassword=$NEXUS_PASSWORD -PmirrorUrl=$NEXUS_MIRROR_URL -PrepositoryUrl=$MAVEN_REPOSITORY_URL jar")
                    }

                    println "Built    with gradle"

                    println "Stashing the workspace..."
                    stash name: STASH_BUILD, includes: '**/*'
                    println "Stashed  the workspace"
                }
            }
        }
    }

    stage('Deploy') {
      openshiftBuild(buildConfig: 'spring-boot', env: [[ name: 'VERSION', value: '0.1.0' ]], showBuildLogs: "true", verbose: verbose)
    }

    stage('Cleanup') {
        unstash STASH_BUILD
        dir('\\complete\\build\\libs') {
            echo sh(returnStdout: true, script: 'ls -la')
        }
    }
}
