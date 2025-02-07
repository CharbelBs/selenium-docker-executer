pipeline {
    agent any

    stages {

        stage('Pull Latest Image') {
            steps {
                retry(3) {  // Retry up to 3 times in case of failure
                    bat "docker pull charbelbsaibess/selenium-docker"
                }
            }
        }

        stage('Start Selenium Grid and run Tests') {
            steps {
                bat 'docker-compose up -d --scale chrome=100'
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
            bat "docker-compose down --remove-orphans"
        }

        failure {
            echo "Build failed! Check logs for details."
        }
    }
}
