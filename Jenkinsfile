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
                    def matcher = commitMessage =~ /^(JEN-\d+)/
                    if (matcher) {
                        env.JIRA_ISSUE_KEY = matcher[0][1]
                        echo "Found Jira Issue Key: ${env.JIRA_ISSUE_KEY}"
                    } else {
                        error("No Jira Issue Key found in commit message.")
                    }
                }
            }
        }
        stage('Update Jira Issue') {
            when {
                expression { env.JIRA_ISSUE_KEY != null }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'jira-token-id', usernameVariable: 'JIRA_USER', passwordVariable: 'JIRA_TOKEN')]) {
                    script {
                        def jiraIssueKey = env.JIRA_ISSUE_KEY
                        def jiraUrl = "https://paormatan-1728284553440.atlassian.net/rest/api/2/issue/${jiraIssueKey}/transitions"

                        sh """
                        curl -X POST -u $JIRA_USER:$JIRA_TOKEN -H "Content-Type: application/json" \
                            --data '{\"transition\": {\"id\": \"31\"}}' \
                            $jiraUrl
                        """
                    }
                }
            }
        }
    }
