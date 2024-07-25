pipeline {
    agent any

    environment {
        TAURUS_VERSION = '1.16.1'  // Specify the Taurus version you need
    }

    stages {
        stage('Preparation') {
            steps {
                // Clone the repository
                git url: 'https://github.com/OmairAhmed111/set.git', branch: 'main'
            }
        }

        stage('Run Performance Test') {
            steps {
                // Run the Taurus test using 'bat' for Windows
                bat 'C://Users//oahmed//AppData//Local//Programs//Python//Python312//Scripts//bzt.exe C://ProgramData//Jenkins//.jenkins//workspace//PerformanceTestGitHub//test.yml'
            }
        }

        stage('Publish Performance Report') {
            steps {
                // Publish JMeter report
                perfReport sourceDataFiles: '**/2024-*/**/*.jtl'
            }
        }

        stage('Archive Results') {
            steps {
                // Debug output to verify files are present
                bat "dir ${env.WORKSPACE}\\**\\results"
                
                // Archive the test results and logs
                archiveArtifacts artifacts: '**/2024-*/**/*.jtl', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            // Archive the test results and logs
            archiveArtifacts artifacts: '**/2024-*/**/*.jtl', allowEmptyArchive: true

            // Generate performance graphs
            perfReport sourceDataFiles: '**/2024-*/**/*.jtl'
        }
    }
}
