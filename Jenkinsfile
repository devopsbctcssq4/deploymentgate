pipeline {
    agent any
    stages {
        stage("Test") {
            steps {
                echo "Deploying to test"
            }
        }
        stage("Stage") {
            steps {
                echo "Deploying to staging"
            }
        }
        stage('Request approval') { // Raise change request
            steps {
                echo 'Raise change request...'
                jiraSendDeploymentInfo(site:'aksservicedesk.atlassian.net',
                        environmentId:'prod-1',
                        environmentName:'prod-1',
                        environmentType:'production',
                        state:"pending",
                        enableGating:true,
			//issueKeys: ['JNG-11'],						
                        serviceIds: [
                          'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlLzE0Yjc1NWYyLTdiYTEtMTFlYi05NjRjLTBhYmUzZjRhNjYwMS9jZmU3YTk0ZS04NjhjLTExZWItOGExNy0wYWJlM2Y0YTY2MDE='
                        ]
                    )
            }
        }
        stage("Approval gate") { // Check change request status
            steps {
                retry(20) { // Poll every 30s for 10min
                    waitUntil { 
                        sleep 30
                        checkGatingStatus(
                          site:'aksservicedesk.atlassian.net', 
                          environmentId:'prod-1'
                        )
                    }
                }   
            }
        }
        stage("Production") {
            steps {
                echo "Deploying to production!!"
            }
            post {
              always {
                sh 'sleep 2'
              }
              // Notify Jira based on deployment step result
              success {
                jiraSendDeploymentInfo (
                        site: 'aksservicedesk.atlassian.net',
                        environmentId: 'prod-1',
                        environmentName: 'prod-1',
                        environmentType: 'production',
                        state: 'successful',
			//issueKeys: ['JNG-11'],
                        serviceIds: [
                          'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlLzE0Yjc1NWYyLTdiYTEtMTFlYi05NjRjLTBhYmUzZjRhNjYwMS9jZmU3YTk0ZS04NjhjLTExZWItOGExNy0wYWJlM2Y0YTY2MDE='
                        ]
                      )
              }
            }
        }
    }
}
