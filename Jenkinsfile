pipeline {
    agent any
    
    tools {
        maven "Maven3"
    }
    stages {
        stage('Clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Git clone') {
            steps {
                git branch: 'feature-1', url: 'https://github.com/Aseemakram19/java-maven-app.git'
            }
        }
        stage('maven war file build') {
            steps {
               sh 'mvn clean package'
            }
        }
        
        stage('Docker images - Push to dockerhub') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolname: 'docker'){
                
                        sh '''docker build -t javamavenappfeature-1 .
                        docker tag javamavenappfeature-1 aseemakram19/javamavenappfeature-1:latest
                        docker push  aseemakram19/javamavenappfeature-1:latest'''
                      } 
                }
            }
        }
        stage('docker container of app') {
            steps {
               sh 'docker run -d -p 9001:8080 --name javamavenappfeature-1_container -t aseemakram19/javamavenappfeature-1:latest'
            }
        }
        
    }
    post {
    always {
        script {
            def buildStatus = currentBuild.currentResult
            def buildUser = currentBuild.getBuildCauses('hudson.model.Cause$UserIdCause')[0]?.userId ?: 'Github User'
            
            emailext (
                subject: "Pipeline ${buildStatus}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>This is a Jenkins maven CICD pipeline status.</p>
                    <p>Project: ${env.JOB_NAME}</p>
                    <p>Build Number: ${env.BUILD_NUMBER}</p>
                    <p>Build Status: ${buildStatus}</p>
                    <p>Started by: ${buildUser}</p>
                    <p>Build URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                """,
                to: 'mohdaseemakram19@gmail.com',
                from: 'mohdaseemakram19@gmail.com',
                replyTo: 'mohdaseemakram19@gmail.com',
                mimeType: 'text/html',
                attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
            )
           }
       }

    }
}
