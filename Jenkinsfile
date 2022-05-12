pipeline{
    agent any
    

    stages{
        stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        
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
