pipeline {
    agent {
        label 'master_master'
    }

    environment {
        WEBHOOK_URL = "your_webhook_url " // to send alert to office365 teams
    }

    triggers {
        cron('H * * * *')
    }

    stages {
        stage('Check Instance State and Logstash Status') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: "id_rsa_logstash", keyFileVariable: 'logstash')]) {
                    script {
                        def instanceId = " your_instance_id" // Replace with your actual instance ID

                        def instanceState = sh(returnStdout: true, script: "aws ec2 describe-instances --instance-ids ${instanceId} --query 'Reservations[0].Instances[0].State.Name' --region your_aws_region").trim()

                        echo "Instance State: ${instanceState}"

                        if (instanceState == '"running"') {
                            echo "EC2 instance is running. Proceeding to check SSH connectivity..."

                            def sshTimeout = 60 // Timeout value in seconds
                            def sshCommand = " your path to the private key used for SSH connection 'echo SSH connection successful'"
                            def sshExitCode = sh(returnStatus: true, script: "timeout ${sshTimeout} ${sshCommand}")

                            if (sshExitCode == 0) {
                                echo "SSH connection successful. Proceeding to check Logstash status."

                                def logstashProcess = sh(returnStatus: true, script: " your path to the private key used for SSH connection 'pgrep -f ^/bin/java.*logstash'")

                                if (logstashProcess == 0) {
                                    echo "Logstash is running"
                                } else {
                                    echo "Logstash is not running"
                                    def logstashStatus = "Logstash is not running"
                                    def message = "Logstash Output:\n $logstashStatus"
                                    def logstashLogFile = "Your path to logstash plain.log file "

                                    // Read the logstash log file and append it to the message
                                    message += "\n\nLogstash Log File:\n"
                                    message += readFile(file: logstashLogFile)

                                    office365ConnectorSend(
                                        webhookUrl: "${env.WEBHOOK_URL}",
                                        message: message,
                                        status: 'Failed'
                                    )
                                }
                            } else {
                                echo "SSH connection timed out. Instance not reachable."
                                error "SSH connection failed. Pipeline failed."
                            }
                        } else {
                            echo "EC2 instance is not running."

                            def instanceFailureMessage = "EC2 instance is not running"
                            def message = "Logstash Output:\n $instanceFailureMessage"

                            office365ConnectorSend(
                                webhookUrl: "${env.WEBHOOK_URL}",
                                message: message,
                                status: 'Failed'
                            )
                        }
                    }
                }
            }
        }
    }

    post {
        failure {
            script {
                def instanceFailureMessage = "EC2 instance is not responding (unresponsive and cannot SSH into it)"
                def message = "Logstash Output:\n $instanceFailureMessage"

                office365ConnectorSend(
                    webhookUrl: "${env.WEBHOOK_URL}",
                    message: message,
                    status: 'Failed'
                )
            }
        }
    }
}