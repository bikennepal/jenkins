What is CICD? and why we need CICD and introduction to CICD.
some standard steps that every organization follows are:
Unit testing
Static code analysis
Code qality/vulnerability
Automation
Reports
deployments

code pushesh to github and after that rest will proceed like building the code testing and deploying.
Jenkins have a feature or custome like way to delpoy code in multiple environments Dev,Stg and Prod.

We have github actions that is advance as we don't need to keep running infrastructe always.

# How to explain the CI/CD process implementation

user-> git->git trigger webhook to jenkins pipeline->jenkins->Continuous integration will start here-> code checkout,build & UT, code scan,sonar or any other solutons,image build,image scan,image registry,after that we can update the manifest files or helm chart with new image-> can use argocd to deploy in the kubernetes platform.


# Jenkins live project to implement above cicd flow.

follow the github repo for more.
So the latest approach for using jenkins is one jenkins master and agent as docker container as containers are ephemeral in nature.

whenever you install any plugin jenkins will run jobs in the configured agent and it is always recommended to restart jenkins whenever plugin is installed.

Simple pipeline to check the docker images:
pipeline{
    agent {
        docker { image 'Hello-World' }
    }
    stages{
        stage ('Verify docker agent'){
            steps{
                sh 'node --version'
            }
            
        }
    }
}

# This is multi-tier pipeline script


pipeline {
  agent none
  stages {
    stage('Back-end') {
      agent {
        docker { image 'maven:3.8.1-adoptopenjdk-11' }
      }
      steps {
        sh 'mvn --version'
      }
    }
    stage('Front-end') {
      agent {
        docker { image 'node:16-alpine' }
      }
      steps {
        sh 'node --version'
      }
    }
  }
}


# for deployinig to kubernetes cluster

pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'f87a34a8-0e09-45e7-b9cf-6dc68feac670', 
                url: 'https://github.com/iam-veeramalla/cicd-end-to-end',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t abhishekf5/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push abhishekf5/cicd-e2e:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'f87a34a8-0e09-45e7-b9cf-6dc68feac670', 
                url: 'https://github.com/iam-veeramalla/cicd-demo-manifests-repo.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'f87a34a8-0e09-45e7-b9cf-6dc68feac670', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/iam-veeramalla/cicd-demo-manifests-repo.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}







