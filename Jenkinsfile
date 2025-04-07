pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'rohith1305/python-flask:${BUILD_NUMBER}' // Unique versioned image
        DOCKER_CREDENTIALS = '7c910bc4-e2e4-48a4-857c-51be93277e96'  // Jenkins Docker Hub Credentials ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/KyathamRohith/python-flask.git'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                        sh "docker push ${DOCKER_IMAGE}"
                        sh "docker logout"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        sh "kubectl apply -f deployment.yaml"
                        sh "kubectl apply -f service.yaml"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Deployment successful! 🚀"
            emailext(
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """<p>The build was <b>successful</b>.</p>
                         <p><b>Job:</b> ${env.JOB_NAME}</p>
                         <p><b>Build:</b> #${env.BUILD_NUMBER}</p>
                         <p><b>URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
                mimeType: 'text/html',
                to: 'kyathamrohith76@gmail.com'
            )
        }

        failure {
            echo "Pipeline failed! ❌"
            emailext(
                subject: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """<p>The build has <b>failed</b>.</p>
                         <p><b>Job:</b> ${env.JOB_NAME}</p>
                         <p><b>Build:</b> #${env.BUILD_NUMBER}</p>
                         <p><b>URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
                mimeType: 'text/html',
                to: 'kyathamrohith76@gmail.com'
            )
        }
    }
}
