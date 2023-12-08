pipeline{
    agent any
     environment {
        APP_NAME = "youtubeapp"
        RELEASE = "1.0.0"
        DOCKER_USER = "saikumarpinisetti"
        DOCKER_PASS = 'Supershot#143'
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        JENKINS_API_TOKEN = credentials("Devops")
    }
    
    stages {
          stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                sh "git branch: 'main', url: 'https://github.com/saikumarpinisetti3/Youtube-clone-app.git'"
            }
        }
}
}
