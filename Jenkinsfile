pipeline{
    
      agent any
    
      tools {
         maven 'M2_HOME'
        }
     environment {
           DOCKER_TAG = getVersion()
        }
    
        stages{
          
          stage('SCM'){
              steps{
                 git branch: 'main', url: 'https://github.com/Luckodjo/ansible-full-pipeline.git'
              }
            }
            
            stage('maven build'){
              steps{
                  sh "mvn clean package"
              }
            }
          
           stage('docker build'){
              steps{
                  sh "docker build . -t luckodjo/web-app:${DOCKER_TAG} "
              }
            }
          
            stage('docker push'){
              steps{
                  withCredentials([string(credentialsId: 'dock-hub', variable: 'dockerhub_passwd')]) {
                  sh "docker login -u luckodjo -p ${dockerhub_passwd}"
                   }
                  sh "docker build . -t luckodjo/web-app:${DOCKER_TAG} "
              }
            }
            
              stage('docker deploy'){
              steps{
                  
                  ansiblePlaybook credentialsId: 'myork-key', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", 
                  installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-container.yml'
                  
                
              }
            }
          
            
            
            
            
        }
        
        
        
    }
    
    def getVersion(){
        def imageversion = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
        return imageversion
    }
 
    
    
    