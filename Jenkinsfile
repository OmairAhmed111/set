pipeline {
    agent any

    environment {
        TAURUS_VERSION = '1.16.1'  // Specify the Taurus version you need
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
                git url: 'https://github.com/OmairAhmed111/set.git', branch: 'main'
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
    }

    post {
        always {
            // Archive the test results and logs
            archiveArtifacts artifacts: '**/*.jtl', allowEmptyArchive: true

            // Generate performance graphs
            perfReport sourceDataFiles: '**/*.jtl'

            // Clean up workspace
            cleanWs()

            // Generate HTML report URL
            script {
                def reportUrl = "${env.BUILD_URL}reports/index.html"
                echo "HTML Report URL: ${reportUrl}"
            }

            // Display results
            script {
                def resultsXml = readFile 'results.xml'
                echo "Results XML: ${resultsXml}"
            }
        }
    }
}
