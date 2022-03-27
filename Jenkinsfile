pipeline {
  agent any

  environment {
    REGISTRY_URL = '352708296901.dkr.ecr.eu-north-1.amazonaws.com/devops_groups_nde_ecr'
    ECR_REGION = 'eu-north-1'
    K8S_NAMESPACE = 'devops-groups-nde'
  }

  stages {
    stage('MNIST Web Server - build'){
      when { branch "main" }
      steps {
          sh '''
          echo building
          '''
      }
    }

    stage('MNIST Web Server - deploy'){
        when { branch "main" }
        steps {
            sh '''
            echo deploying
            '''
        }
    }


    stage('MNIST Predictor - build'){
        when { branch "main" }
        steps {
            sh '''
            IMAGE="mnist-predictor:0.0.1"
            cd ml_model
            aws ecr get-login-password --region $ECR_REGION | docker login --username AWS --password-stdin ${REGISTRY_URL}
            docker build -t ${IMAGE} .
            docker tag ${IMAGE} ${REGISTRY_URL}/${IMAGE}
            docker push ${REGISTRY_URL}/${IMAGE}
            '''
        }
    }

    stage('MNIST Predictor - deploy'){
        when { branch "main" }
        steps {
            sh '''
            cd infra/k8s
            IMG_NAME=mnist-predictor:0.0.${BUILD_NUMBER}
            # replace registry url and image name placeholders in yaml
            sed -i "s/{{REGISTRY_URL}}/$REGISTRY_URL/g" mnist-predictor.yaml
            sed -i "s/{{K8S_NAMESPACE}}/$K8S_NAMESPACE/g" mnist-predictor.yaml
            sed -i "s/{{IMG_NAME}}/$IMG_NAME/g" mnist-predictor.yaml
            # get kubeconfig creds (k8s auth)
            aws eks --region eu-north-1 update-kubeconfig --name devops-apr21-k8s
            # apply to your namespace
            kubectl apply -f mnist-predictor.yaml -n $K8S_NAMESPACE
            '''
        }
    }
  }
}
