pipeline {
    agent any

    environment {
        TAURUS_VERSION = '1.16.1'  // Specify the Taurus version you need
        WORKSPACE_PATH = 'C://ProgramData//Jenkins//.jenkins//workspace//PerformanceTestGitHub'
        TAURUS_PATH = 'C://Users//ahmedoma//AppData//Local//Programs//Python//Python312//Scripts//bzt.exe'
        TAURUS_CONFIG = "${WORKSPACE_PATH}//test.yml"
        REPORT_DIR = "${WORKSPACE_PATH}//reports"
    }

    stages {
        stage('Preparation') {
            steps {
                // Clone the repository
                git url: 'https://github.com/OmairAhmed111/set.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install Taurus (if not installed)
                script {
                    if (!fileExists(env.TAURUS_PATH)) {
                        bat 'pip install bzt'
                    }
                }
            }
        }

        stage('Run Performance Test') {
            steps {
                // Run the Taurus test using 'bat' for Windows
                bat 'C://Users//ahmedoma//AppData//Local//Programs//Python//Python312//Scripts//bzt.exe C://ProgramData//Jenkins//.jenkins//workspace//PerformanceTestGitHub//test.yml'
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
                        // Publish JUnit test results
                        junit 'results.xml'
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
            // Archive the test results and logs
            archiveArtifacts artifacts: '**/*.jtl', allowEmptyArchive: true

            // Generate performance graphs
            perfReport sourceDataFiles: '**/*.jtl'
        }
    }
}
