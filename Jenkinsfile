pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timeout(time: 20, unit: 'MINUTES') // Prevents builds from running too long
        buildDiscarder(logRotator(numToKeepStr: '10')) // Keep only last 10 builds
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()  // Cleans up workspace before the build
            }
        }

        stage('Pull Latest Image') {
            steps {
                retry(3) {  // Retry up to 3 times in case of failure
                    bat "docker pull charbelbsaibess/selenium-docker"
                }
            }
        }

        stage('Start Selenium Grid') {
            steps {
                bat 'docker-compose up -d --scale chrome=4 hub chrome firefox'
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
            bat "docker-compose down --remove-orphans"
        }

        failure {
            echo "Build failed! Check logs for details."
        }
    }
}
