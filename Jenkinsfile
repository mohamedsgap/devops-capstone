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
                      sh "aws eks --region us-west-2 update-kubeconfig --name ridiculous-unicorn-1595428959"
                      sh "kubectl config use-context arn:aws:eks:us-east-2:263546620847:cluster/ridiculous-unicorn-1595428959"
                      sh "kubectl set image deployments/capstone-project-cloud-devops capstone-project-cloud-devops=mohamedsgap/mohd-capstone"
                      sh "kubectl apply -f deployment/deployment.yml"
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
