pipeline{
    agent any
    
     tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
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
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        
        stage("Docker Build "){
            steps{
                script{
                     
                     sh "docker build --build-arg REACT_APP_RAPID_API_KEY=e3430d2467mshe6b3f78568544e5p1a1522jsn591ac3682c0f -t ${APP_NAME}:${IMAGE_TAG} ."
                    sh "docker image tag ${APP_NAME}:${IMAGE_TAG} saikumarpinisetti/${APP_NAME}:${IMAGE_TAG}"
                     sh "docker image tag ${APP_NAME}:${IMAGE_TAG} saikumarpinisetti/${APP_NAME}:latest"
                    }
                }
            }
        stage("PUSH IMAGE TO DOCKER HUB"){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {

                    sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
                    sh "docker image push saikumarpinisetti/${APP_NAME}:${IMAGE_TAG}"
                    sh "docker image push saikumarpinisetti/${APP_NAME}:latest"
                    }   
                }
            }
        }
         stage('Cleanup Artifacts') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
         stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's|${APP_NAME}.*|${APP_NAME}:${IMAGE_TAG}|g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }
        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git remote set-url origin git@github.com:saikumarpinisetti3/Youtube-clone-app.git

                   git config --global user.name "saikumarpinisetti3"
                   git config --global user.email "saikumarpinisetti3@gmail.com"
                   git add deployment.yaml
                   git commit -m "Updated Deployment Manifest"
                """
               withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    // Use the Git credentials
                        gitTool 'Default'  // Assuming 'Default' is your Git tool name
                        sh "git push https://github.com/saikumarpinisetti3/Youtube-clone-app.git main"
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
