pipeline{
    agent any
    
     tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
        APP_NAME = "youtubeapp"
        RELEASE = "1.0.0"
        DOCKER_USER = "saikumarpinisetti"
        DOCKER_PASS = 'Supershot#143'
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
   
    stages {
          stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/saikumarpinisetti3/Youtube-clone-app.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=youtube \
                    -Dsonar.projectKey=youtube '''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token' 
                }
            } 
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage("Docker Build "){
            steps{
                script{
                     
                     sh "docker build --build-arg REACT_APP_RAPID_API_KEY=e3430d2467mshe6b3f78568544e5p1a1522jsn591ac3682c0f -t ${APP_NAME}:${IMAGE_TAG} ."
                   
                     sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
                    }
                }
            }
    }

    post {
    always {
        echo 'Slack Notifications'
        slackSend (
            channel: '#jenkins-youtube',
            message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        )
    }
        }
}
