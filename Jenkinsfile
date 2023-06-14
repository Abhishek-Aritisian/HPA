pipeline {
    agent any
    tools{
        maven 'maven'
    }
    
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Abhishek-Aritisian/devops-automation.git']]])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t abhishekp006/kubernetes:$BUILD_NUMBER .'
                }
            }
        }
        stage('Push image to hub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u abhishekp006 -p ${dockerhubpwd}'
                        
                    }
                    sh 'docker push abhishekp006/kubernetes:$BUILD_NUMBER'
                }
            }
        }
        stage('Deploy to K8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'kubernetes')
                }
            }
        }
        stage('Create HPA') {
            steps {
                script {
                    kubernetesScale(name: 'my-deployment', replicas: 3)
                    sh 'kubectl autoscale deployment my-deployment --cpu-percent=70 --min=2 --max=5 --namespace=my-namespace --name=my-hpa'
                }
            }
        }
    }    
}
