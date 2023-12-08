pipeline{
    agent any
     tools{
        jdk 'jdk17'
        nodejs 'node16'
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
        stage('static code analysis') {
    steps {
        script {
            withSonarQubeEnv('sonar-server') {
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
