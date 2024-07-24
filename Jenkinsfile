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

        stage('Install Dependencies') {
            steps {
                // Install Taurus
                sh 'pip install bzt==${env.TaURUS_VERSION}'

                // Ensure JMeter is installed (adjust the path to match your installation)
                sh '''
                if [ ! -d /opt/apache-jmeter-5.4.1 ]; then
                    wget https://downloads.apache.org//jmeter/binaries/apache-jmeter-5.4.1.tgz
                    tar -xzf apache-jmeter-5.4.1.tgz -C /opt
                fi
                '''
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
            archiveArtifacts artifacts: '/results/**', allowEmptyArchive: true
        }
    }
}
