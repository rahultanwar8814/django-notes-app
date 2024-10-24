@Library("Shared") _

pipeline {
    agent any
    environment{
        TRIVY_OUTPUT = 'trivy-results' // Directory where scan results will be saved
        SCAN_PATH = '.'  // Specify the path to scan, '.' for current directory
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

        stage('Install Dependencies') {
            steps {
                script {
                    sh 'npm install' // For Node.js dependencies
                    sh 'pip install -r requirements.txt' // For Python dependencies
                }
            }
        }

        stage('Filesystem Scan with Trivy') {
            steps {
                script {
                    sh '''
                    mkdir -p ${TRIVY_OUTPUT}
                    trivy fs --exit-code 1 --severity HIGH,CRITICAL --output ${TRIVY_OUTPUT}/trivy-results.txt ${SCAN_PATH}
                    '''
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
