pipeline {
    agent any

    environment {
        TAURUS_VERSION = '1.16.1'  // Specify the Taurus version you need
        GIT_URL = 'https://github.com/OmairAhmed111/set.git'  // Use the Git URL directly
        JMETER_PATH = 'C://Users//ahmedoma//AppData//Local//Programs//Python//Python312//Scripts//bzt.exe'
        TEST_YML_PATH = 'C://ProgramData//Jenkins//.jenkins//workspace//PerformanceTestGitHub//test.yml'
        GITHUB_TOKEN = credentials('github-token') // GitHub App token stored in Jenkins credentials
        GITHUB_REPOSITORY = 'OmairAhmed111/set' // Replace with your GitHub repository
        CHECK_RUN_NAME = 'Jenkins CI'
    }

    options {
        timestamps()  // Add timestamps to the console output
        timeout(time: 1, unit: 'HOURS')  // Set a timeout for the pipeline
        disableConcurrentBuilds()  // Prevent concurrent builds
    }

    stages {
        stage('Initialize Check Run') {
            steps {
                script {
                    def response = httpRequest acceptType: 'APPLICATION_JSON',
                                              contentType: 'APPLICATION_JSON',
                                              httpMode: 'POST',
                                              requestBody: """
                                              {
                                                "name": "${env.CHECK_RUN_NAME}",
                                                "head_sha": "${env.GIT_COMMIT}",
                                                "status": "in_progress",
                                                "started_at": "${new Date().format("yyyy-MM-dd'T'HH:mm:ss'Z'", TimeZone.getTimeZone('UTC'))}"
                                              }
                                              """,
                                              url: "https://api.github.com/repos/${env.GITHUB_REPOSITORY}/check-runs",
                                              customHeaders: [[name: 'Authorization', value: "Bearer ${env.GITHUB_TOKEN}"]]
                    def checkRun = new groovy.json.JsonSlurper().parseText(response.content)
                    env.CHECK_RUN_ID = checkRun.id
                }
            }
        }
        stage('Preparation') {
            steps {
                // Clone the repository
                git url: "${GIT_URL}", branch: 'main', credentialsId: 'your-credentials-id'
            }
        }
        stage('Run Performance Test') {
            steps {
                // Run the Taurus test using 'bat' for Windows
                bat "${JMETER_PATH} ${TEST_YML_PATH}"
            }
        }
        stage('Publish Performance Report') {
            steps {
                // Publish JMeter report
                perfReport sourceDataFiles: '**/*.jtl'
            }
        }
        stage('Publish Results') {
            steps {
                script {
                    bat 'dir'
                    if (fileExists('results.xml')) {
                        // Publish JUnit test results
                        junit 'results.xml'
                    } else {
                        echo 'results.xml not found!'
                    }
                }
            }
        }
        stage('Publish HTML Report') {
            steps {
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'reports',
                    reportFiles: 'index.html',
                    reportName: 'Taurus Performance Report'
                ])
            }
        }
    }

    post {
        always {
            script {
                def conclusion = currentBuild.result == 'SUCCESS' ? 'success' : 'failure'
                httpRequest acceptType: 'APPLICATION_JSON',
                            contentType: 'APPLICATION_JSON',
                            httpMode: 'PATCH',
                            requestBody: """
                            {
                              "status": "completed",
                              "completed_at": "${new Date().format("yyyy-MM-dd'T'HH:mm:ss'Z'", TimeZone.getTimeZone('UTC'))}",
                              "conclusion": "${conclusion}"
                            }
                            """,
                            url: "https://api.github.com/repos/${env.GITHUB_REPOSITORY}/check-runs/${env.CHECK_RUN_ID}",
                            customHeaders: [[name: 'Authorization', value: "Bearer ${env.GITHUB_TOKEN}"]]
            }

            // Archive the test results and logs
            archiveArtifacts artifacts: '**/*.jtl', allowEmptyArchive: true

            // Generate performance graphs
            perfReport sourceDataFiles: '**/*.jtl'

            // Clean up workspace
            cleanWs()
        }
    }
}
