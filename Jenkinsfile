pipeline {
  agent any

  environment {
    REGISTRY_URL = '352708296901.dkr.ecr.eu-north-1.amazonaws.com'
    ECR_REGION = 'eu-north-1'
    K8S_NAMESPACE = 'devops-groups-nde'
    def emailBody = "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n"
    def emailSubject = "${env.JOB_NAME} - Build# ${env.BUILD_NUMBER}"

  }
  stages {
    stage('MNIST Web Server - Build '){
      when { anyOf {branch "main";branch "noams";branch "danielItzakian"} }
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
        post {
         success {
            echo 'MNIST Web Server Build was successful '
            /*emailext(mimeType: 'text/html', subject: emailSubject+'Test Results', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], body: emailBody*/
                }
         failure {
            echo 'MNIST Web Server Build failed'
            emailext(mimeType: 'text/html', subject: emailSubject+' MNIST Web Server Build failed', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], body: emailBody+'MNIST Web Server Build failed')
        }
      }
    }

    stage('MNIST Web Server - Deploy'){
        when { anyOf {branch "main";branch "noams";branch "danielItzakian"} }
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
        post {
//            always {
//                jiraSendDeploymentInfo environmentId: 'us-prod-1', environmentName: 'eu-north-1', environmentType: 'staging'
//            }
            success {
                echo 'MNIST Web Server Deploy was successful '
                /*emailext(mimeType: 'text/html', subject: emailSubject+'Test Results', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], body: emailBody)*/
            }
            failure {
                echo 'MNIST Web Server Deploy failed'
                emailext(mimeType: 'text/html', subject: emailSubject+' MNIST Web Server Deploy failed', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], body: emailBody+'MNIST Web Server Deploy failed')
        }
}
    }


    stage('MNIST Predictor - Build'){
        when { anyOf {branch "main";branch "noams";branch "danielItzakian"} }
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
        post {
             success {
                echo 'MNIST Predictor Build was successful'
                /*emailext(mimeType: 'text/html', subject: emailSubject+'Test Results', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], body: emailBody)*/
                    }
             failure {
                echo 'MNIST Predictor Build failed'
                emailext(mimeType: 'text/html', subject: emailSubject+' MNIST Predictor Build failed', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], body: emailBody+'MNIST Predictor Build failed')
        }
      }
    }

    stage('MNIST Predictor - Deploy'){
        when { anyOf {branch "main";branch "noams";branch "danielItzakian"} }
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
        post {
            success {
                echo 'MNIST Predictor Deploy was successful '
                emailext(mimeType: 'text/html', subject: emailSubject+' MNIST Predictor Deploy was successful', recipientProviders: [[$class: 'DevelopersRecipientProvider']] , body: emailBody )
                    }
            failure {
                echo 'MNIST Predictor Deploy failed'
                emailext(mimeType: 'text/html', subject: emailSubject+' MNIST Predictor Deploy failed', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], body: emailBody+'MNIST Predictor Deploy failed')
        }
      }
    }
    stage('MNIST Grafana Dashboard - Deploy'){
    steps {
        sh '''
        cd infra/k8s
        aws eks --region eu-north-1 update-kubeconfig --name devops-apr21-k8s
        kubectl apply -f grafana-dashboard.yaml
        '''
    }
    post {
        failure {
            echo 'MNIST Grafana Dashboard Deploy failed'
            emailext(mimeType: 'text/html', subject: emailSubject+' MNIST Predictor Deploy failed', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], body: emailBody+'MNIST Predictor Deploy failed')
        }
      }
    }
    stage('Cleanup local images') {
    steps {
      echo '=== Cleaning local image ==='
        script{
        sh '''
        echo '=== Delete the local docker images ==='
        final="${REGISTRY}/${IMG}"
        docker rmi -f $final
        docker system prune -f
        echo 'Removed local image Successfully'
            '''
            }
        }
    }
  }
}