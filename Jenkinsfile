pipeline {
  agent any
  environment {
    AWS_ACCOUNT_ID = "407730735276"
    AWS_DEFAULT_REGION = "us-east-1"
    IMAGE_REPO_NAME = "app"
    IMAGE_TAG = "latest"
    REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
  }

  stages {

    stage('Logging into AWS ECR') {
      steps {
        script {
          sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
        }

      }
    }

    stage('Cloning Git') {
      steps {
        checkout([$class: 'GitSCM', branches: [
          [name: '*/main']
        ], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [
          [credentialsId: '', url: 'https://github.com/sagarainapur/C7_Project.git']
        ]])
      }
    }

    // Building Docker images
    stage('Building image') {
      steps {
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }

    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
      steps {
        script {
          sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
          sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }

    // Uploading Docker images into AWS ECR
    stage('Deploy to ECR') {
      steps {
        script {
            
          sh "ssh -i StrictHostKeyChecking=False -i ~/.ssh/id_rsa ubuntu@10.0.2.34 && docker stop 407730735276.dkr.ecr.us-east-1.amazonaws.com/app:latest && docker rmi 407730735276.dkr.ecr.us-east-1.amazonaws.com/app:latest && docker run -itd -p 8080:8080 407730735276.dkr.ecr.us-east-1.amazonaws.com/app:latest"
        }
      }
    }

  }
