pipeline {
    agent any

    parameters {
        string(name: 'IMAGE', description: 'Image to deploy')
        string(name: 'VERSION', description: 'Version tag')
    }

    environment {
        APP_NAME = "cpp-demo"
        CONTAINER_NAME = "cpp-demo-prod"
    }

    stages {

        stage('Pull Image') {
            steps {
                sh """
                    set -e
                    echo "Pulling ${params.IMAGE}"
                    docker pull ${params.IMAGE}
                """
            }
        }

        stage('Stop Old Container') {
            steps {
                sh """
                    docker rm -f ${CONTAINER_NAME} || true
                """
            }
        }

        stage('Deploy Container') {
            steps {
                sh """
                    set -e
                    echo "Deploying ${params.IMAGE}"

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p 8081:8080 \
                        ${params.IMAGE} \
                        /build/app
                """
            }
        }

        stage('Health Check') {
            steps {
                sh """
                    set -e
                    echo "Waiting for service..."

                    sleep 2

                    docker exec ${CONTAINER_NAME} /build/app || exit 1

                    echo "Deployment OK"
                """
            }
        }
    }

    post {
        success {
            echo "DEPLOY SUCCESS ✅"
        }

        failure {
            echo "DEPLOY FAILED ❌ (rollback needed)"
        }
    }
}
