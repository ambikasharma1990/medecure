pipeline {
agent any 
tools {
maven 'maven'
}

stages {
stage("Git Checkout"){
steps{
git 'https://github.com/ambikasharma1990/medecure.git'
 }
 }
stage('Build the application'){
steps{
echo 'cleaning..compiling..testing..packaging..'
sh 'mvn clean install package'
 }
 }
 
stage('Publish HTML Report'){
steps{
    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/medicureproject/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
 }
}
stage('Docker build image') {
              steps {
                  
                  
                  sh 'sudo docker build -t rambikasharma/medicure:latest . '
              
                }
            }
stage('Docker login and push') {
              steps {
                   withCredentials([string(credentialsId: 'docpass', variable: 'docpasswd')]) {
                  sh 'sudo docker login -u rambikasharma -p ${docpasswd} '
                  sh 'sudo docker push rambikasharma/medicure:latest'
                  }
                }
        }    
 stage (' setting up Kubernetes with terraform '){
            steps{

                dir('terraform_files'){
                sh 'terraform init'
                sh 'terraform validate'
                sh 'terraform apply --auto-approve'
                sh 'sleep 20'
                }
               
            }
        }
stage('deploy to application to kubernetes'){
steps{
sh 'sudo chmod 600 ./terraform_files/project2.pem'    
sh 'sudo scp -o StrictHostKeyChecking=no -i ./terraform_files/project2.pem medicure-deployment.yml ubuntu@54.226.125.9:/home/ubuntu/'
sh 'sudo scp -o StrictHostKeyChecking=no -i ./terraform_files/project2.pem medicure-service.yml ubuntu@54.226.125.9:/home/ubuntu/'
script{
try{
sh 'ssh -i ./terraform_files/project2.pem ubuntu@54.226.125.9 kubectl apply -f .'
}catch(error)
{
sh 'ssh -i ./terraform_files/project2.pem ubuntu@54.226.125.9 kubectl apply -f .'
}
}
}
}
}
}
