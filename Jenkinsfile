@Library("Shared") _

pipeline {
    agent any

    stages {
        stage("Testing Library Connection") {
            steps {
                script {
                    test_connection()
                }
            }
        }

        stage("Code") {
            steps {
                script {
                    clone("https://github.com/rahultanwar8814/django-notes-app.git", "master")
                }
            }
        }

        stage("Build") {
            steps {
                script {
                    docker_build("notes-app", "latest", "rahultanwar")
                }
            }
        }

        stage("Push to DockerHub") {
            steps {
                script {
                    docker_push("notes-app", "latest", "rahultanwar")
                }
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploying the code"
                sh "docker-compose up -d"
            }
        }
    }
}
