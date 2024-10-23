pipeline {
    agent any

    environment
        EC2_USER = 'ubuntu'          // EC2 instance user
        EC2_HOST = '13.201.22.8'     // EC2 instance public IP
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the Git repository
                git url: 'https://github.com/muthu512/spring.git', branch: 'master'
            }
        }

        stage('Build') {
            steps {
                // Build the Spring Boot application
                sh "${MAVEN_HOME}/bin/mvn clean package"
            }
        }

        stage('Deploy') {
            steps {
                // Use credentials to securely access the SSH key
                sshagent(['your-ssh-credentials-id']) { // Replace with your Jenkins credentials ID
                    // Copy the JAR to EC2 instance
                    sh "scp -o StrictHostKeyChecking=no target/hello-world-0.0.1-SNAPSHOT.jar ${EC2_USER}@${EC2_HOST}:/home/ec2-user/"
                    // Run the JAR in the background
                    sh "ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} 'nohup java -jar /home/ec2-user/hello-world-0.0.1-SNAPSHOT.jar > app.log 2>&1 &'"
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace
        }
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
