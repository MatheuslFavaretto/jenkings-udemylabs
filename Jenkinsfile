pipeline {
    agent any
    tools { go 'go-1.19' } // Comes from the jenkins global config

    stages {
        stage('Build') {
            environment {
                ENV = "${env.BRANCH_NAME == 'master' ? 'PROD' : 'DEV'}"
            }
            steps {
                sh 'bash scripts/build.sh' // Run the build.sh asset
            }
        }
        stage('Test') {
            steps {
                sh 'bash scripts/test.sh' // Run the test.sh asset
            }
        }
        stage('Deploy') {
            environment {
                BRANCH = "${env.BRANCH_NAME}" // Needed by the deployment script
            }
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

