pipeline {
  agent any

  environment {
    REGISTRY_URL = '352708296901.dkr.ecr.eu-north-1.amazonaws.com'
    ECR_REGION = 'eu-north-1'
    K8S_NAMESPACE = 'devops-groups-nde'
  }

  stages {
    stage('MNIST Web Server - build'){
      steps {
          sh '''
            IMAGE="mnist-webserver:0.0.${BUILD_NUMBER}"
            cd webserver
            aws ecr get-login-password --region $ECR_REGION | docker login --username AWS --password-stdin ${REGISTRY_URL}
            docker build -t ${IMAGE} .
            docker tag ${IMAGE} ${REGISTRY_URL}/${IMAGE}
            docker push ${REGISTRY_URL}/${IMAGE}
            '''
      }
    }


    stage('MNIST Web Server - deploy'){
        steps {
            sh '''
            cd infra/k8s
            IMG_NAME=mnist-webserver:0.0.${BUILD_NUMBER}
            # replace registry url and image name placeholders in yaml
            sed -i "s/{{REGISTRY_URL}}/$REGISTRY_URL/g" mnist-webserver.yaml
            sed -i "s/{{K8S_NAMESPACE}}/$K8S_NAMESPACE/g" mnist-webserver.yaml
            sed -i "s/{{IMG_NAME}}/$IMG_NAME/g" mnist-webserver.yaml
            # get kubeconfig creds (k8s auth)
            aws eks --region eu-north-1 update-kubeconfig --name devops-apr21-k8s
            # apply to your namespace
            kubectl apply -f mnist-webserver.yaml -n $K8S_NAMESPACE
            '''
        }
    }

    stage('Run safety check'){
      when { branch "danielItzakian" }
      steps {
          sh '''
          /user/local/bin/safety check
          '''
      }
    }



    stage('MNIST Predictor - build'){
        steps {
            sh '''
            IMAGE="mnist-predictor:0.0.${BUILD_NUMBER}"
            cd ml_model
            aws ecr get-login-password --region $ECR_REGION | docker login --username AWS --password-stdin ${REGISTRY_URL}
            docker build -t ${IMAGE} .
            docker tag ${IMAGE} ${REGISTRY_URL}/${IMAGE}
            docker push ${REGISTRY_URL}/${IMAGE}
            '''
        }
    }

    stage('MNIST Predictor - deploy'){
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
