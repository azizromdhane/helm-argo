pipeline {
    agent any

    // Vérifie toutes les 5 minutes les changements dans le repo
    triggers { pollSCM('H/5 * * * *') }

    environment {
        IMAGE_SERVER = 'azizromdhane/mern-server'   // Ton DockerHub serveur
        IMAGE_CLIENT = 'azizromdhane/mern-client'   // Ton DockerHub client
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@gitlab.com:azizrom001/mern-app.git',
                    credentialsId: 'gitlab_ssh' // Clé SSH Jenkins
            }
        }

        stage('Build + Push SERVER') {
            when { changeset "server/**/*" }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',  // Jenkins Credentials DockerHub
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh '''
                    echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                    docker build -t $IMAGE_SERVER:${BUILD_NUMBER} server
                    docker push $IMAGE_SERVER:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Build + Push CLIENT') {
            when { changeset "client/**/*" }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh '''
                    echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                    docker build -t $IMAGE_CLIENT:${BUILD_NUMBER} client
                    docker push $IMAGE_CLIENT:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Scan SERVER with Trivy') {
            when { changeset "server/**/*" }
            steps {
                sh '''
                echo "=== Scanning Docker image with Trivy ==="
                docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
                    aquasec/trivy image $IMAGE_SERVER:${BUILD_NUMBER} > trivy_report.txt || true
                echo "=== Vulnerability Summary ==="
                grep -E "CRITICAL|HIGH" trivy_report.txt || echo "No CRITICAL or HIGH vulnerabilities found."
                '''
            }
        }
    }

    post {
        always {
            echo "🧹 Cleaning Docker system..."
            sh 'docker system prune -af || true'
            archiveArtifacts artifacts: 'trivy_report.txt', allowEmptyArchive: true
        }
    }
}
