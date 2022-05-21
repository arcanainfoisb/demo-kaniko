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
            sh 'mvn dependency:get -Dartifact=mysql:mysql-connector-java:8.0.29'
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
                             --destination=internal-registry.tkg.mobilink.net.pk/wso2-dev/kaniko/jenkins-build:${BUILD_NUMBER}
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
