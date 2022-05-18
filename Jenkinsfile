pipeline {


  agent {
    kubernetes {
      yamlFile 'builder.yaml'
    }
  }

  stages {
    
        stage('Maven build stage') {
      steps {
        container('maven') {
          script {
            sh 'hostname'
          }
        }
      }
    }

    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          script {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=internal-registry.tkg.mobilink.net.pk/wso2-dev/kaniko:${BUILD_NUMBER}
            '''
          }
        }
      }
    }

    stage('Deploy App to Kubernetes') {
      steps {
        container('kubectl') {          
          withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
            sh 'kubectl get nodes'
          }
        }
      }
    }

  }
}
