    pipeline {
        agent any
        tools { go 'go-1.19' } // Comes from the jenkins global config
        ...
    }
    
        pipeline {
        ...
        environment {
            ENV = "${env.BRANCH_NAME == 'master' ? 'PROD' : 'DEV'}"
        }
        ...
    }

        pipeline {
        ...
        stages {
            stage('Build') {
                steps {
                    sh 'bash scripts/build.sh' // Run the build.sh asset
                }
            }
            stage('Test') {
                steps {
                    sh 'bash scripts/test.sh' // Run the test.sh asset
                }
            }
        }
    }
    
    pipeline {
    environment {
        ...
        BRANCH = "${env.BRANCH_NAME}" // Needed by the deployment script
    }
    stages {
        ...
        stage('Deploy') {
            when {
                anyOf {
                    branch 'master';
                    branch 'develop'
                }
            }
            steps {
                sh 'export JENKINS_NODE_COOKIE=do_not_kill ; bash scripts/deploy.sh'
            }
        }
    }
}