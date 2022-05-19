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
            sh 'echo "kaniko" > /tmp/shared/demo.txt'
          }
        }
      }
    }

    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          script {
            sh 'cat /tmp/shared/demo.txt'
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
          script {
            sh '''
            export KUBECTL_VSPHERE_PASSWORD=jInfra@890
            kubectl vsphere login --server=10.50.49.1 --insecure-skip-tls-verify --vsphere-username tkgs-wso2-dev-herman@vsphere.local --tanzu-kubernetes-cluster-name tkc-dev-wso2-ns001 --tanzu-kubernetes-cluster-namespace tkgs-dev-wso2-ns001
            kubectl get nodes
            '''
          }
//           withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
//             sh 'kubectl get nodes'
//           }
        }
      }
    }

  }
}
