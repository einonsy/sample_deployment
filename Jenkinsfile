pipeline {
   agent any

   environment {
      JUNIT = 'true'
      // Yarn needs to write to the HOME directory, without this it will attempt to write to /root
      // Cypress requires `$HOME` to be an absolute path, so we reference the WORKSPACE variable
      HOME = "${env.WORKSPACE}"
      RELEASE_TAG = "${env.BRANCH_NAME}-r${BUILD_NUMBER}"
      nameSpace = "epaas-test-b-${BUILD_NUMBER}"
      script {
         
      }
   }

   stages {
      stage('Environment review') { steps { script {
            sh 'kubectl version'
            sh 'filename='output.txt'
               while read project; do 
               sh "docker build -t einonsy/$project:${BRANCH_NAME}-latest" 
               done < $filename'
         }
      }
      }


   // TODO Unit Test


      stage('Build Images') {
         steps {
            sh "docker build -t einonsy/podinfo:${env.BUILD_NUMBER} ."
         }
      }

      stage('push images') {
         steps {
            withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
            sh "docker login -u ${env.dockerHubUSER} -p ${env.dockerHubPassword}"
            sh "docker push einonsy/podinfo:${env.BUILD_NUMBER}"
            echo "COMMITTER_EMAIL: ${env.AUTHOR}"
         }
            }
      }
      stage('Docker Remove Image') {
         steps {
            sh "docker rmi einonsy/podinfo:${env.BUILD_NUMBER}"
         }
      }
      
      stage('Deploy') {
         steps {
            sh "kubectl create namespace ${nameSpace}"
            sh 'cat deployment.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" |  sed "s/{{nameSpace}}/$nameSpace/g" | kubectl apply -f -'
            sh 'kubectl apply -f service.yaml'
            
         }
      }


   }
}
