pipeline{
    agent any
   
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
        stage('static code analysis') {
    steps {
        script {
            withSonarQubeEnv(credentialsId: 'sonar-token') {
                sh "npm run sonar-scanner"
            }
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
}
}
