@Library('my-shared-lib@main') _

pipeline {
    agent any

    environment {
        ATTENDANCE_REPO = 'https://github.com/OT-MICROSERVICES/attendance-api.git'
        NOTIFICATION_REPO = 'https://github.com/OT-MICROSERVICES/notification-worker.git'
        SLACK_CHANNEL = '#jenkins-notification'
        EMAIL_RECIPIENTS = 'snaatak.alpha@gmail.com'
        PRIORITY = 'P0'
        ATTENDANCE_REPORT_URL = "${env.BUILD_URL}pip-audit_20Report_20-_20Attendance_20API/"
        NOTIFICATION_REPORT_URL = "${env.BUILD_URL}pip-audit_20Report_20-_20Notification_20Worker/"
    }

    stages {
        stage('Install Packages') {
            steps {
                script {
                    new org.snaatak.AuditSteps(this).installPackages()
                }
            }
        }

        stage('Clone Repositories') {
            steps {
                script {
                    new org.snaatak.AuditSteps(this).cloneRepositories(env.ATTENDANCE_REPO, env.NOTIFICATION_REPO)
                }
            }
        }

        stage('Audit Attendance') {
            steps {
                script {
                    new org.snaatak.AuditSteps(this).auditAttendance()
                }
            }
        }

        stage('Audit Notification') {
            steps {
                script {
                    new org.snaatak.AuditSteps(this).auditNotification()
                }
            }
        }

        stage('Publish Reports') {
            steps {
                script {
                    new org.snaatak.AuditSteps(this).publishReports()
                }
            }
        }
    }

    post {
        always {
            script {
                def notifier = new org.snaatak.AuditNotifier()
                notifier.sendNotification(
                    this,
                    currentBuild.currentResult,
                    env.PRIORITY,
                    env.SLACK_CHANNEL,
                    env.EMAIL_RECIPIENTS,
                    env.ATTENDANCE_REPORT_URL,
                    env.NOTIFICATION_REPORT_URL
                )
            }
        }
    }
}
