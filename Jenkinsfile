pipeline{
    agent any
    

        
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
