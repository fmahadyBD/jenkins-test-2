## Note
- My Jenkins is running on port 8080.
- So, I mapped the Docker container port 8080 to host port 8085 to avoid conflicts.

```groovy
pipeline {
    agent any

    tools {
        maven '3.8.7'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/fmahadyBD/jenkins-test-2',
                        credentialsId: 'cd240911-1c41-4b4b-9739-3ebb7b875a1b'
                    ]]
                ])
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("myapp")
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh 'docker run -d -p 8085:8080 myapp'
                }
            }
        }
    }
}
