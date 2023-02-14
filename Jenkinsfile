pipeline {
    agent{ label 'QA Environment'}

    stages {
        l;o
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
        stage ('upload') {
    gitlabCommitStatus("upload") {
      def server = Artifactory.server "artifactory@ibsrv02"
      def buildInfo = Artifactory.newBuildInfo()
      buildInfo.env.capture = true
      buildInfo.env.collect()
      def uploadSpec = """{
        "files": [
          {
            "pattern": "**/target/*.jar",
            "target": "libs-snapshot-local"
          }, {
            "pattern": "**/target/*.pom",
            "target": "libs-snapshot-local"
          }, {
            "pattern": "**/target/*.war",
            "target": "libs-snapshot-local"
          }
        ]
      }"""
      server.upload spec: uploadSpec, buildInfo: buildInfo

      buildInfo.retention maxBuilds: 10, maxDays: 7, deleteBuildArtifacts: true
     
      server.publishBuildInfo buildInfo
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
