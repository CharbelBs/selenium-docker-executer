pipeline {
    agent any
     options {
         disableConcurrentBuilds()
         timeout(time: 20, unit: 'MINUTES') // Prevents Jenkins from running too long
         buildDiscarder(logRotator(numToKeepStr: '10')) // Keep only last 10 builds
         cleanWs()  // Cleans up workspace before build
     }

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

        stage('Start Selenium Grid and Run Tests') {
            steps {
                bat "docker-compose up -d --scale chrome=3"
            }
        }
    }


    post {
        always {
            script {
                echo "Archiving test results..."
                archiveArtifacts artifacts: "output/index.html", onlyIfSuccessful: false
            }

            echo "Shutting down Selenium Grid..."
            bat "docker-compose down"
        }

        failure {
            echo "Build failed! Check logs for details."
        }
    }
}
