pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'charbelbsaibess/selenium-docker'
    }

    stages {

        stage('Pull Latest Image') {
            steps {
                retry(3) {  // Retry up to 3 times in case of failure
                    bat "docker pull ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Start Selenium Grid') {
            steps {
                bat 'docker-compose up -d hub chrome firefox'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'docker-compose up search-module'
            }
        }
    }

    post {
        always {
            script {
                echo "Archiving test results..."
                archiveArtifacts artifacts: "output/**", onlyIfSuccessful: false
            }

            echo "Shutting down Selenium Grid..."
            bat "docker-compose down"
        }

        failure {
            echo "Build failed! Check logs for details."
        }
    }
}
