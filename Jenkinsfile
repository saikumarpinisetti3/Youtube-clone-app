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
        stage('BUILD'){
            steps{
                script{
                    sh "sudo apt install npm -y"
                    sh "npm run build"
                }
            }
        }
}
}
