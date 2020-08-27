pipeline {
     agent any
     stages {
         stage('Build') {
              steps {
                  sh 'echo Building...'
              }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.'
              }
         }
         stage('Build Docker Image') {
              steps {
                  sh 'docker build -t mohd-capstone .'
              }
         }
         stage('Push Docker Image') {
              steps {
                  withDockerRegistry([url: "", credentialsId: "docker-hub"]) {
                      sh "docker tag mohamedsgap/mohd-capstone"
                      sh 'docker push mohamedsgap/mohd-capstone'
                  }
              }
         }
         stage('Deploying') {
              steps{
                  echo 'Deploying to AWS...'
                  withAWS(credentials: 'aws', region: 'us-west-2') {
                      sh "aws eks --region us-west-2 update-kubeconfig --name mohd-capstone"
                      sh "kubectl config use-context arn:aws:eks:us-west-2:864485235780:cluster/mohd-capstone"
                      sh "kubectl set image deployments/mohd-capstone mohd-capstone=mohamedsgap/mohd-capstone"
                      sh "kubectl apply -f scripts/deploy.yml"
                      sh "kubectl get nodes"
                      sh "kubectl get deployment"
                      sh "kubectl get pod -o wide"
                      sh "kubectl get service/mohd-capstone"
                  }
              }
        }
        stage("Cleaning up") {
              steps{
                    echo 'Cleaning up...'
                    sh "docker system prune"
              }
        }
     }
}
