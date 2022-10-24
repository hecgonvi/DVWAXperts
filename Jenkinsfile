environment {
    region = "us-east-1"
    docker_repo_uri = "547850580937.dkr.ecr.us-east-1.amazonaws.com/test-app"
    task_def_arn = "arn:aws:ecs:us-east-1:xxxxxxxxxxxx:task-definition/first-run-task-definition"
    cluster = "default"
    exec_role_arn = "arn:aws:iam::547850580937:instance-profile/jenkins"
}
stages {
    
            stage('Logging into AWS ECR') {
            steps {
                script {
                sh """aws ecr-public get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${REPOSITORY_URI} """
                }
                 
            }
        } 
    
    stage('Clone repository') { 
            steps { 
                script{
                checkout scm
                }
            }
        }  
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}-${env.BUILD_NUMBER}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG}-${env.BUILD_NUMBER} ${REPOSITORY_URI}:$IMAGE_TAG-${env.BUILD_NUMBER}"""
                sh """docker push ${REPOSITORY_URI}:${IMAGE_TAG}-${env.BUILD_NUMBER}"""
         }
        }
      }
      stage('Deploy'){
            steps {
                 sh 'sed -i "s/<TAG>/${IMAGE_TAG}-${BUILD_NUMBER}/" deployment.yml'
                 sh 'kubectl apply -f deployment.yml'
                 /*
                 //If you are sure this deployment is already running and want to change the container image version, then you can use:
                 sh 'kubectl set image deployments/dvwa 371571523880.dkr.ecr.us-east-2.amazonaws.com/dvwaxperts:${BUILD_NUMBER}'*/
            }
        } 
    }
}
