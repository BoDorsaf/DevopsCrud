pipeline{
    agent any
    environment {
        localhost = "http://172.10.0.140/"
        dockerImage = "achat"
        registry= "dora12334/bdrepo"
        registryCredential = "dockerhub"
    }
    stages {
        stage('Clone source code from Git') {
            steps {
                echo "Cloning Project from GitHub; Branch : "
                git branch: 'main',
                url: ' https://github.com/BoDorsaf/DevopsCrud.git'
             }
        }
    stage('Unit Testing') {
            steps {
              
                sh 'mvn test' 
            }
        }
        stage('Integration Testing') {
            steps {
             
                sh  'mvn verify'
            }
        }
        stage('MVN CLEAN') {
            steps {
                sh 'mvn -version'
                sh 'mvn clean'
            }
        }
         stage('SonarQube') {
            steps {
                sh"mvn package -B -DskipTests sonar:sonar -Dsonar.host.url=http:172.10.0.140:9000  -Dsonar.login=0205abd0bb8f5d088beeaee5d73c45869f61c6f7"
            }
        }
        stage('NEXUS') {
            steps {
                sh 'mvn clean package deploy:deploy-file -DgroupId=tn.esprit -DartifactId=achat -Dversion=1.0 -DgeneratePom=true -Dpackaging=war -DrepositoryId=deploymentRepo -Durl=http://172.10.0.140:8081/repository/maven-releases/ -Dfile=target/achat-1.0.jar'
            }
        }
        stage('Docker Image') { 
            steps { 
                script { 
       
                    dockerImage = docker.build registry
                    
                }
            } 
        }
        stage('PUSH DOCKERHUB') { 
            steps { 
                script { 
                       
						  docker.withRegistry ('', registryCredential ) {
							  dockerImage.push()
                        }
                }    
                
            } 
            
         }
         /*stage('delete IMG') {
            steps {
                sh "docker rmi $registry:latest"
            }
        }*/
           stage('DOCKER-COMPOSE') {
            steps {
                sh 'docker-compose down --remove-orphans'
                sh 'docker-compose up -d'
                //sh 'docker restart spring-boot-docker-container'
            }
        }
    }
        post {

        always {

     

            emailext   to: "dorsaf.belouja@esprit.tn",
			subject: "jenkins build:${currentBuild.currentResult}: ${env.JOB_NAME}",
     			body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}"

            
        }

    }
    
}
