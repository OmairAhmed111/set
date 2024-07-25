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

        stage('Install BZT') {
            steps {
                // Install BZT
                bat 'pip install bzt==${TAURUS_VERSION}'
            }
        }

        stage('Run Performance Test') {
            steps {
                // Run the Taurus test using 'bat' for Windows
                bat 'bzt test.yml'
            }
        }

        stage('Publish Performance Report') {
            steps {
                // Assuming you use the JMeter plugin for Jenkins to publish the report
                // Adjust the path to your actual JMeter results
                perfReport sourceDataFiles: '**/*.jtl'
            }
        }
    }

    post {
        always {
            // Archive the test results and logs
            archiveArtifacts artifacts: 'results/**', allowEmptyArchive: true
        }
    }
}
