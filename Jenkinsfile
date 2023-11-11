pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t yamiannephilim/portfolio:latest .'
            }
        }

        stage('Push') {
            steps {
                withDockerRegistry(credentialsId: 'docker_hub', url: 'https://index.docker.io/v1/') {
                    sh 'docker push yamiannephilim/portfolio'
                }
            }
        }

        stage('Clean') {
            steps {
                script {
                    def containerId = sh(returnStdout: true, script: 'docker ps -aqf "name=portfolio"').trim()
                    if (containerId) {
                        sh "docker stop $containerId"
                        sh "docker rm $containerId"
                    }
                }
            }
        }

        stage('Run') {
            steps {
                sh 'docker container stop portfolio || echo "this container does not exist"'
                sh 'docker network create yan || echo "this network exist"'
                sh 'echo y | docker container prune'
                sh 'docker run --name portfolio --network yan --restart=unless-stopped -d yamiannephilim/portfolio:latest'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
