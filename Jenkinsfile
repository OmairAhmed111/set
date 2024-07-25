pipeline {
    agent any

    environment {
        TAURUS_VERSION = '1.16.1' // Specify the Taurus version you need
    }

    stages {
        stage('Preparation') {
            steps {
                // Clone the repository
                git url: 'https://github.com/OmairAhmed111/set.git', branch: 'main'
            }
        }

        stage('Install Taurus') {
            steps {
                // Install Taurus if not already installed
                bat '''
                    if not exist "%APPDATA%\\bzt" (
                        pip install bzt==${TAURUS_VERSION}
                    )
                '''
            }
        }

        stage('Run Performance Test') {
            steps {
                // Run the Taurus test
                bat 'bzt test.yml'
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
