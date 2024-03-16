def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
]

pipeline{
    agent any
    environment {
        DOCKER_IMAGE_NAME = 'ridhampatel/react-private'
    }
    tools{
        nodejs "nodeJS"
    }
    stages{
        stage("Fetch Code"){
            steps{
                git branch: 'main', url:'https://github.com/ridhampatel24/jenkins_demo.git'
            }
            post {
                success {
                    slackSend channel: '#jenkinscicd',
                    color: COLOR_MAP[currentBuild.currentResult],
                    message: "*${currentBuild.currentResult}:* Fetch Code Complete."
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}", " .")
                }
            }

            post {
                success {
                    slackSend channel: '#jenkinscicd',
                    color: COLOR_MAP[currentBuild.currentResult],
                    message: "*${currentBuild.currentResult}:* Docker Image is Build. Uploading Image..."
                }
            }
        }        

        stage('Upload to Docker Hub') {
            steps {
                script {
                    docker.withRegistry( '', 'dockercred') {  
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push('latest')
                    }
                }
            }

            post {
                success {
                    slackSend channel: '#jenkinscicd',
                    color: COLOR_MAP[currentBuild.currentResult],
                    message: "*${currentBuild.currentResult}:* Docker Image Uploaded Succesfully to Docker Hub"
                }
            }
        }

    }

    post {
        always {
            slackSend channel: '#jenkinscicd',
            color: COLOR_MAP[currentBuild.currentResult],
            message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}