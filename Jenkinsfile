pipeline {
    agent any
    tools { go 'go-1.19' } // Comes from the jenkins global config

    environment {
        ENV = "${env.BRANCH_NAME == 'master' ? 'PROD' : 'DEV'}"
        BRANCH = "${env.BRANCH_NAME}" // Needed by the deployment script
    }

    stages {
        stage('Send initial notification') {
            when {
                anyOf {
                    branch 'master';
                    branch 'develop'
                }
            }
            steps {
                slackSend channel: '#general',
                          message: "Build for job ${env.JOB_NAME} has started - (<${env.BUILD_URL}|Open>)"
            }
        }

        stage('Scan for secrets') {
            steps {
                sh '''
                    curl -LO https://github.com/zricethezav/gitleaks/releases/download/v8.9.0/gitleaks_8.9.0_linux_x64.tar.gz
                    tar -xzf gitleaks_8.9.0_linux_x64.tar.gz
                    ./gitleaks protect -v // Scan for commonly leaked secrets
                    rm -rf gitleaks*
                '''
            }
        }

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

        stage('Deploy') {
            when {
                anyOf {
                    branch 'master';
                    branch 'develop'
                }
            }
            steps {
                script {
                    withEnv(['JENKINS_NODE_COOKIE=do_not_kill']) {
                        sh 'bash scripts/deploy.sh'
                    }
                }
            }
        }
    }

    post {
        always {
            slackSend channel: '#general',
                      color: "${currentBuild.currentResult == 'SUCCESS' ? 'good' : 'danger'}",
                      message: "Build for job ${env.JOB_NAME} finished with status ${currentBuild.currentResult} - (<${env.BUILD_URL}|Open>)"
        }
    }
}



