pipeline {
    agent any
    environment {
        //be sure to replace "......" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "hassaansiddiqi20/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', '64b261f6-22a6-4832-a9af-ebdf1b25b55b') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                sh 'kubectl apply -f train-schedule-kube.yml'
                )                
            }
        }
    }
}
