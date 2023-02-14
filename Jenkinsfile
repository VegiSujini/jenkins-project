pipeline {
    agent{ label 'QA Environment'}

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
        stage('checking out from github')
        {
            steps
            {
                git 'https://github.com/SaiiChegondi/jenkins-example.git'
            }
        }
       
        stage('mvn compiling')
        {
            steps{
            bat 'mvn compile'
            
            }
            
        }
       
      
        stage('mvn test')
        {
            steps{
            bat 'mvn test'
        }
        }
        stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat 'mvn sonar:sonar'
                }
            }
        }
         stage("Quality gate") {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
        stage('mvn package')
        {
            steps{
            bat 'mvn clean package'
        }
        }
         stage('archiving archifacts')
            {
                steps{
                archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
                }
            }
       
       
       stage ('Upload file') {
            steps {
                rtUpload (
                   
                    serverId:SERVER_ID,
                    spec: """{
                            "files": [
                                    {
                                        "pattern": "CaseStudyPipeline/pom.xml",
                                        "target": "libs-snapshot-local"
                                    }
                                ]
                            }"""
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: SERVER_ID
                )
            }
        }
       
       
       
}
 post {
        always {
            echo 'This will always run'
             junit(testResults: 'target/surefire-reports/*.xml', allowEmptyResults : true)
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            mail bcc: '', body: "${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "chegondinagasai@gmail.com";
        }
 }

}

