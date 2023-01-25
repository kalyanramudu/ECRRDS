pipeline {
    agent any
    environment {
        registry = "836327461657.dkr.ecr.us-east-1.amazonaws.com/onlinbookstore"

        
    }

    stages {
        stage('Git Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'kalyangithub', url: 'https://github.com/kalyanramudu/ECRRDS.git']])
    
                
                
                
                
            }
        }
        
        
        stage('maven build') {
            steps {
                sh "mvn clean install"
                
                
            }
        }
        
        

        
    stage('Building image') {
      steps{
        script {
          sh 'sudo chmod 666 /var/run/docker.sock'
          dockerImage = docker.build registry
        }
      }
    }
    

   
    
    stage('tag your image') {
      steps{
        script {
          sh 'docker tag 836327461657.dkr.ecr.us-east-1.amazonaws.com/onlinbookstore 836327461657.dkr.ecr.us-east-1.amazonaws.com/onlinbookstore:${BUILD_NUMBER}'
        }
      }
    }
    
    
    
    stage('Login to ECR') {
      steps{
        script {
            
            sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 836327461657.dkr.ecr.us-east-1.amazonaws.com'
        }
      }
      
    }
        
    
    
    stage('Image to ECR') {
      steps{
        script {
          
           sh 'docker push 836327461657.dkr.ecr.us-east-1.amazonaws.com/onlinbookstore:${BUILD_NUMBER}'
        }
      }
    }
    
                stage('Register newtaskDef') {
            steps {
            sh '''\
            TASK_DEFINITION=$(aws ecs describe-task-definition --task-definition BooksECR --region "us-east-1")
            NEW_IMAGE=836327461657.dkr.ecr.us-east-1.amazonaws.com/onlinbookstore:${BUILD_NUMBER}    
            NEW_TASK_DEFINTIION=$(echo $TASK_DEFINITION | jq --arg IMAGE "$NEW_IMAGE" '.taskDefinition | .containerDefinitions[0].image = $IMAGE | del(.taskDefinitionArn) | del(.revision) | del(.status) | del(.requiresAttributes) | del(.compatibilities)')
            NEW_TASK_INFO=$(aws ecs register-task-definition --region "us-east-1" --cli-input-json "$NEW_TASK_DEFINTIION")
            NEW_REVISION=$(echo $NEW_TASK_INFO | jq '.taskDefinition.revision')
            aws ecs update-service --cluster Javacluster \
                       --service Javaservice \
                       --task-definition BooksECR:${NEW_REVISION}
            
                  
            '''

            }
        }
        
        
    

    stage('Clean Workspace') {
        steps {
            always {
                cleanWs()
                }
                    
                
                
                
            }
        }


}
    
    
    
    
}

