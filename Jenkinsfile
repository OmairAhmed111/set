pipeline {
    agent any

    environment {
        TAURUS_VERSION = '1.16.1'  // Specify the Taurus version you need
        GIT_URL = 'https://github.com/OmairAhmed111/set.git'  // Use the Git URL directly
        JMETER_PATH = 'C://Users//ahmedoma//AppData//Local//Programs//Python//Python312//Scripts//bzt.exe'
        TEST_YML_PATH = 'C://ProgramData//Jenkins//.jenkins//workspace//PerformanceTestGitHub//test.yml'
    }

    options {
        timestamps()  // Add timestamps to the console output
        timeout(time: 1, unit: 'HOURS')  // Set a timeout for the pipeline
        disableConcurrentBuilds()  // Prevent concurrent builds
    }

    stages {
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
                    reportDir: 'C://ProgramData//Jenkins//.jenkins//workspace//PerformanceTestGitHub//reports',
                    reportFiles: 'index.html',
                    reportName: 'Taurus Performance Report'
                ])
            }
        }
    }

    post {
        always {
            // Archive the test results and logs
            archiveArtifacts artifacts: '**/*.jtl', allowEmptyArchive: true

            // Generate performance graphs
            perfReport sourceDataFiles: '**/*.jtl'

            // Clean up workspace
            cleanWs()
        }
    }
}
