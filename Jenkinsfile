pipeline{
    agent any
    
    stages{
        stage('SAST'){
            steps{
                withSonarQubeEnv('sonar-pro'){
                sh 'mvn sonar:sonar'
                sh 'cat target/sonar/report-task.txt'
            }
        }
    }
}
}
