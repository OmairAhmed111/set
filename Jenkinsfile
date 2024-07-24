pipeline {
    agent any

    environment {
        TAURUS_VERSION = '1.16.1'  // Specify the Taurus version you need
    }

    stages {
        stage('Preparation') {
            steps {
                // Install Taurus if not already installed
                sh '''
                    if ! command -v bzt &> /dev/null
                    then
                        pip install bzt==$TAURUS_VERSION
                    fi
                '''
                // Clone the repository
                git url: 'https://github.com/test/set.git', branch: 'main'
            }
        }

        stage('Run Performance Test') {
            steps {
                // Run the Taurus test
                sh 'bzt test.yml'
            }
        }

        stage('Publish Performance Report') {
            steps {
                // Publish JMeter report
                perfReport sourceDataFiles: '**/*.jtl'
            }
        }
    }

    post {
        always {
            // Archive the test results and logs
            archiveArtifacts artifacts: '**/results/**', allowEmptyArchive: true
            // Clean up workspace after the build
            cleanWs()
        }
    }
}
