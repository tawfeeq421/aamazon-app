pipeline{
    agent any
    tools{
        jdk 'JDK17'
        nodejs 'NODE16'
    }
    environment{
        DOCKER_IMAGE = 'tawfeeq421/amazon'
        DOCKER_TAG ="${BUILD_NUMBER}"
        SCANNER_HOME = tool 'sonar'
    }
    stages{
        stage('Clean Workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout SCM'){
            steps{
                git branch: 'main', url: 'https://github.com/tawfeeq421/aamazon-app.git'
            }
        }
        stage('Install Dependency'){
            steps{
                sh 'npm install'
            }
        }
        stage('SonarQube Scan'){
            steps{
                withSonarQubeEnv('sonarserver'){
                    sh """
                    ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=Amazon \
                    -Dsonar.projectKey=Amazon
                    """
                }
            }
        }
        stage('Quality Gate'){
            steps{
                timeout(time: 1, unit: 'HOURS'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Trivy FS Scan'){
            steps{
                sh '''
                trivy fs . \
                --severity HIGH,CRITICAL \
                --format table \
                --no-progress \
                -o trivy-report.txt
                '''
            }
        }
        stage('Docker Build'){
            steps{
                script{
                    def app = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}", ".")
                }
            }
        }
        stage('Image Scan & Push'){
            steps{
                script{
                    def app = docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}")
                    sh """
                    trivy image \
                    --severity HIGH,CRITICAL \
                    --format table \
                    --no-progress \
                    -o trivy-image-report.txt \
                    ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-cred'){
                        app.push()
                        app.push('latest')
                    }
                }
            }
        }
    }
    post {
        always{
            archiveArtifacts artifacts: 'trivy-report.txt, trivy-image-report.txt', fingerprint: true
        }
        success{
            slackSend(
                channel: "amazon",
                color: "good",
                message: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}\nCheck Docker Image ${DOCKER_IMAGE}:${DOCKER_TAG}"
            )
        }
        failure{
            slackSend(
                channel: "amazon",
                color: "danger",
                message: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n See logs ${env.BUILD_URL}"
            )
        }
    }
}