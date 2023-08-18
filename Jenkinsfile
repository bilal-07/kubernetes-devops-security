pipeline {
  agent any
  stages {
      stage('Build Artifact - Maven') {
        steps {
          sh "mvn clean package -DskipTests=true"
          archive 'target/*.jar'
        }
      }

      stage('Unit Tests - JUnit and Jacoco') {
        steps {
          sh "mvn test"
        }
        post {
          always {
            junit 'target/surefire-reports/*.xml'
            jacoco execPattern: 'target/jacoco.exec'
          }
        }
      }

      stage('Docker Build & Push') {
        steps {
          withDockerRegistry(url:'', credentialsId: 'docker-hub'){
            sh 'docker build -t bilalaslam0313/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker push bilalaslam0313/numeric-app:""$GIT_COMMIT""'
          }
        }
      }

      stage('K8S Deployment - DEV') {
        steps {
          withKubeConfig([credentialsId: 'kubeconfig']) {
            sh sh "sed -i 's#replace#bilalaslam0313/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yml"
              sh 'kubectl apply -f k8s_deployment_service.yml'
           }
        }
      }
    }
}
