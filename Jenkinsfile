@Library("Shared") _

pipeline {
    agent any
    environment{
        SONAR_HOME = tool "sonar"
    }

    stages {
        stage("Testing Library Connection") {
            steps {
                script {
                    test_connection()
                }
            }
        }
        stage("Workspace cleanup"){
            steps{
                script{
                    cleanWs()
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

        stage("Trivy: Filesystem scan"){
            steps{
                script{
                    trivy_scan()
                }
            }
        }

        stage("OWASP: Dependency check"){
            steps{
                script{
                    owasp_dependency()
                }
            }
        }
        
        stage("SonarQube: Code Analysis"){
            steps{
                script{
                    sonarqube_analysis("Sonar","wanderlust","wanderlust")
                }
            }
        }
        
        stage("SonarQube: Code Quality Gates"){
            steps{
                script{
                    sonarqube_code_quality()
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
