@Library('my-shared-library') _

pipeline {

    agent any

    parameters {
        choice(name: 'action', choices: ['create', 'delete'], description: 'Choose create/Destroy')
        string(name: 'ImageName', defaultValue: 'javapp', description: "name of the docker build")
        string(name: 'ImageTag', defaultValue: 'v1', description: "tag of the docker build")
        string(name: 'DockerHubUser', defaultValue: 'lpsandeep24', description: "name of the Application")
    }

    stages {
         
        stage('Git Checkout') {
            when { expression { params.action == 'create' } }
            steps {
                gitCheckout(
                    branch: "main",
                    url: "https://github.com/Lpsandeep01/Java_app_3.0.git"
                )
            }
        }

        stage('Unit Test maven') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnTest()
                }
            }
        }

        stage('Integration Test maven') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnIntegrationTest()
                }
            }
        }

        stage('Static code analysis: Sonarqube') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    def SonarQubecredentialsId = 'sonarqube-api'
                    staticCodeAnalysis(SonarQubecredentialsId)
                }
            }
       }

       stage('Quality Gate Status Check : Sonarqube') {
           when { expression { params.action == 'create' } }
           steps{
               script{
                   def SonarQubecredentialsId = 'sonarqube-api'
                   QualityGateStatus(SonarQubecredentialsId)
               }
           }
       }

       stage('Maven Build : maven') {
           when { expression { params.action == 'create' } }
           steps{
               script{
                   mvnBuild()
               }
           }
       }

       stage('Docker Image Build') {
           when { expression { params.action == 'create' } }
           steps{
               script{
                   dockerBuild(params.ImageName, params.ImageTag, params.DockerHubUser)
               }
           }
       }

       stage('Docker Image Scan: trivy ') {
           when { expression { params.action == 'create' } }
           steps{
               script{
                   dockerImageScan(params.ImageName, params.ImageTag, params.DockerHubUser)
               }
           }
       }

       stage('Docker Image Push : DockerHub ') {
           when { expression { params.action == 'create' } }
           steps{
               script{
                   dockerImagePush(params.ImageName, params.ImageTag, params.DockerHubUser)
               }
           }
       }

       // Corrected closing of this block
       stage('Docker Image Cleanup : DockerHub ') {
         when{expression{params.action=='create'}}
         steps{
             script{dockerImageCleanup(params.ImageName,params.ImageTag,params.DockerHubUser)}
         }// Missing closing brace added here
      }// Missing closing brace added here

      // The rest of your stages can now follow as normal
      // ...

    }// Stages closing brace

}// Pipeline closing brace
