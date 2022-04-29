pipeline {
    agent any 
    stages {
        stage('Compile and Clean') { 
            steps {

                sh "mvn clean compile"
            }
        }
       

        stage('deploy') { 
            steps {
                sh "mvn package"
            }
        }


        stage('Build Docker image'){
            steps {
                //sh 'sudo chmod +x var/run/docker.sock'
                sh 'docker build -t  vkr1998/docker_jenkins_springboot:${BUILD_NUMBER} .'
            }
        }

        stage('Docker Login'){
            
            steps {
                 withCredentials([string(credentialsId: 'Docker_id', variable: 'Dockerpwd')]) {
                    sh "docker login -u vkr1998 -p ${Dockerpwd}"
                }
            }                
        }

        stage('Docker Push'){
            steps {
                sh 'docker push vkr1998/docker_jenkins_springboot:${BUILD_NUMBER}'
            }
        }
        
        stage('Docker deploy'){
            steps {
               
                sh 'docker run -itd -p  8081:8080 vkr1998/docker_jenkins_springboot:${BUILD_NUMBER}'
            }
        }

        
        stage('Archving') { 
            steps {
                 archiveArtifacts '**/target/*.jar'
            }
        }
        stage('Deploy to k8s') {
            steps {
                sshagent(['k8s']) {
                    sh "scp -o StrictHostKeyChecking=no Deployment.yaml root@34.125.45.126:/root"
                    //script {
                        //try {
                    sh "ssh root@34.125.45.126 kubectl create -f Deployment.yaml"
                        //}
                        
                        //catch(error) {
                            //sh "ssh root@34.125.45.126 kubectl create -f Deployment.yaml"
                        //}
                    //}
                }
            }
        }     
            
            
              
    }
}

