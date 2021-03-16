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
                        environmentId:'JNG-11',
                        environmentName:'JNG-11',
                        environmentType:'production',
                        state:"pending",
                        enableGating:true,
						issueKeys: ['JNG-11'],						
                        serviceIds: [
                          ''
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
                          environmentId:'JNG-11'
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
                        environmentId: 'JNG-11',
                        environmentName: 'JNG-11',
                        environmentType: 'production',
                        state: 'successful',
						issueKeys: ['JNG-11'],
                        serviceIds: [
                          ''
                        ]
                      )
              }
            }
        }
    }
}
