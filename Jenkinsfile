pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/matanlevipaor/jenkins-jira.git', credentialsId: 'github-token', branch: 'main'
            }
        }
        stage('Extract Jira Issue') {
            steps {
                script {
                    def commitMessage = sh(returnStdout: true, script: 'git log -1 --pretty=%B').trim()
                    def matcher = commitMessage =~ /^#(\w+-\d+)/
                    if (matcher) {
                        env.JIRA_ISSUE_KEY = matcher[0][1]
                        echo "Found Jira Issue Key: ${env.JIRA_ISSUE_KEY}"
                    } else {
                        error("No Jira Issue Key found in commit message.") 
                    }
                }
            }
        }
    }
}
