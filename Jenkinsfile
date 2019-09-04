pipeline {

    environment {
        DOCKER_API_VERSION="1.23"      

        //tag = readFile('commit-id').replace("\n", "").replace("\r", "")
        tag = "${BUILD_NUMBER}"
        appName = "hello-kenzan"
        registryHost = "127.0.0.1:30400/"
        imageName = "${registryHost}${appName}:${tag}"
        //BUILDIMG="${imageName}"
    }
   

    agent any

    stages {
      stage("Clone repository") {
          steps {
            checkout scm
            sh "git rev-parse --short HEAD > commit-id"
          }
      }  

      stage("Build") {
          steps {
            sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
          }
      }
          
    
      stage("Push") {
          steps {
            sh "docker push ${imageName}"
            sh "docker push ${registryHost}${appName}:latest"
          }  
      }
    

      stage("Deploy") {
          steps {
              script{
                echo "appName: ${appName}"
                sh "ls -als applications/${appName}/k8s"                
                kubernetesDeploy configs: "applications/${appName}/k8s/*.yaml", kubeconfigId: 'kenzan_kubeconfig'
              }
          }
      }

    }
}