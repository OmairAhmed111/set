pipeline {
    agent any

    environment {
        TAURUS_VERSION = '1.16.1'  // Specify the Taurus version you need
        PYTHON_URL = 'https://www.python.org/ftp/python/3.9.6/python-3.9.6-amd64.exe'  // URL to the Python installer
    }

    stages {
        stage('Install Python and Taurus') {
            steps {
                script {
                    // Define installation commands
                    def installPythonCmd = """
                        powershell -command "Start-Process -FilePath 'curl' -ArgumentList '-o python-installer.exe ${env.PYTHON_URL}' -NoNewWindow -Wait"
                        powershell -command "Start-Process -FilePath 'python-installer.exe' -ArgumentList '/quiet InstallAllUsers=1 PrependPath=1' -NoNewWindow -Wait"
                        powershell -command "Remove-Item -Force python-installer.exe"
                    """

                    def installTaurusCmd = """
                        python -m pip install --upgrade pip
                        python -m pip install bzt
                    """

                    // Run the installation commands
                    bat installPythonCmd
                    bat installTaurusCmd

                    // Verify installation
                    bat 'python --version'
                    bat 'pip --version'
                    bat 'bzt -v'
                }
            }
        }

        stage('Preparation') {
            steps {
                // Clone the repository
                git url: 'https://github.com/test/set.git', branch: 'main'
            }
        }

        stage('Verify File Presence') {
            steps {
                // Check if the file exists
                bat 'if not exist "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\PerformanceTestGitHub\\test.yml" (echo File not found! & exit 1) else (echo File exists)'
            }
        }

        stage('Run Performance Test') {
            steps {
                // Run the Taurus test using 'bat' for Windows and log output
                bat 'bzt C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\PerformanceTestGitHub\\test.yml > bzt_output.log 2>&1'
                // Check if bzt execution was successful
                bat 'type bzt_output.log'
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
            archiveArtifacts artifacts: 'results/**', allowEmptyArchive: true
            archiveArtifacts artifacts: 'bzt_output.log', allowEmptyArchive: true
        }
    }
}
