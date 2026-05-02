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
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        stage('Trivy Image Scan'){
            steps{
                sh """
                trivy image \
                --severity HIGH,CRITICAL \
                --format table \
                --no-progress \
                -o trivy-image-report.txt \
                ${DOCKER_IMAGE}:${DOCKER_TAG}
                """
            }
        }
        stage('Docker Push'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId : 'docker-cred',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]){
                    echo $PASS | docker login -u $USER --password-stdin 

                    docker push ${DOCKER_IMAGE}:${DOCKER_TAG}

                    docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest

                    docker push ${DOCKER_IMAGE}:latest
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
                channel: "#amazon",
                color: "good",
                message: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}\nCheck Docker Image ${DOCKER_IMAGE}:${DOCKER_TAG}"
            )
        }
        failure{
            slackSend(
                channel: "#amazon",
                color: "danger",
                message: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n See logs ${env.BUILD_URL}"
            )
        }
    }
}