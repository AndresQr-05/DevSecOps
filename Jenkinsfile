// =====================================================================
// SIT223/SIT753 - 7.1C - Part 2, Task 2: DevSecOps + Extended Email
// Notification. This EXTENDS the Part 1 - Task 2 pipeline: same 5
// stages, plus an email sent (with the console log attached) at the
// end of the "Run Tests" stage and at the end of the "NPM Audit
// (Security Scan)" stage, as required by the brief.
//
// Prerequisites (see the execution guide for full steps):
//  1. Install the "Email Extension Plugin" in Jenkins (Manage Jenkins >
//     Plugins), if it is not already installed.
//  2. Configure SMTP under Manage Jenkins > Configure System >
//     Extended E-mail Notification (e.g., Gmail SMTP + an "app
//     password", NOT your normal Gmail password).
// =====================================================================
pipeline {
    agent any

    environment {
        NOTIFY_EMAIL = 'andresqr88@gmail.com'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AndresQr-05/DevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'npm test || exit /b 0'
            }
            post {
                always {
                    emailext(
                        subject: "Jenkins - 'Run Tests' stage - ${currentBuild.currentResult} - Build #${env.BUILD_NUMBER}",
                        body: "The 'Run Tests' stage of job ${env.JOB_NAME} finished with status: ${currentBuild.currentResult}.\n\nSee the attached console log for full details.",
                        to: "${env.NOTIFY_EMAIL}",
                        attachLog: true
                    )
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0'
            }
            post {
                always {
                    emailext(
                        subject: "Jenkins - 'NPM Audit (Security Scan)' stage - ${currentBuild.currentResult} - Build #${env.BUILD_NUMBER}",
                        body: "The 'NPM Audit (Security Scan)' stage of job ${env.JOB_NAME} finished with status: ${currentBuild.currentResult}.\n\nSee the attached console log for full details (this includes the list of known CVEs found by npm audit).",
                        to: "${env.NOTIFY_EMAIL}",
                        attachLog: true
                    )
                }
            }
        }
    }
}
