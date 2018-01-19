node {
    stage('Prepare') {
        println "Preparing the build..."

        STASH_GIT_REPO="git-repo"
        STASH_BUILD="build-result"

        //echo sh(returnStdout: true, script: 'mkdir -p gittest')
        //dir('\\gittest') {
        //    git credentialsId: 'ci-secret-github-ssh', url: 'git@github.com:spring-guides/gs-spring-boot.git'
        //    GIT_REF = sh returnStdout: true, script: 'git rev-parse --verify HEAD'
        //    stash name: STASH_GIT_REPO, includes: '**/*'    
        //}
        
        println "Stashing git repo..."
        dir('../workspace@script'){
            GIT_REF = sh returnStdout: true, script: 'git rev-parse --verify HEAD'
            stash name: STASH_GIT_REPO, includes: '**/*'
        }
        println "Stashed  git repo: 'git-repo'"
        println "Prepared  the build"
    }

    stage('Build') {
        echo sh(returnStdout: true, script: 'env')
    
    
        podTemplate(name: 'jenkins-slave-gradle',
                    cloud: 'openshift',
                    containers: [
            containerTemplate(name: 'jnlp',
                            image: 'ci/jenkins-slave-gradle',
                            resourceRequestCpu: '500m',
                            resourceLimitCpu: '4000m',
                            resourceRequestMemory: '1024Mi',
                            resourceLimitMemory: '4096Mi',
                            slaveConnectTimeout: 180,
                            envVars: [
                                    envVar(key: 'MYSQL_ALLOW_EMPTY_PASSWORD', value: 'true')
                                ])
        ]) {
            node('jenkins-slave-gradle'){
                container('jnlp'){
                    println "Unstashing '${STASH_GIT_REPO}'..."
                    unstash STASH_GIT_REPO
                    println "Unstaheed  '${STASH_GIT_REPO}'"
                    //echo sh(returnStdout: true, script: 'env')
                    //echo sh(returnStdout: true, script: 'ls -la')
                    dir('\\complete') {
                        echo sh(returnStdout: true, script: 'gradle -DnexusUsername=$NEXUS_USER -DnexusPassword=$NEXUS_PASSWORD jar')
                    }

                    println "Built    with gradle"

                    println "Stashing the workspace..."
                    stash name: STASH_BUILD, includes: '**/*'
                    println "Stashed  the workspace"
                }
            }
        }
    }
    
    stage('Cleanup') {
        unstash STASH_BUILD
        dir('\\complete\\build\\libs') {
            echo sh(returnStdout: true, script: 'ls -la')
        }
    }
} 
