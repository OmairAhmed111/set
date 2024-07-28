pipeline {
    agent any

    environment {
        TAURUS_VERSION = '1.16.1'  // Specify the Taurus version you need
        WORKSPACE_PATH = 'C://ProgramData//Jenkins//.jenkins//workspace//PerformanceTestGitHub'
        TAURUS_PATH = 'C://Users//ahmedoma//AppData//Local//Programs//Python//Python312//Scripts//bzt.exe'
        TAURUS_CONFIG = "${WORKSPACE_PATH}//test.yml"
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
                bat "${env.TAURUS_PATH} ${env.TAURUS_CONFIG}"
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
                    if (fileExists('results.xml')) {
                        // Publish JUnit test results
                        junit 'results.xml'
                    } else {
                        echo 'results.xml not found!'
                    }
                }
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
