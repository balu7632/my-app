pipeline{
    agent any
    
    environment{
        PATH = "/opt/maven/apache-maven-3.8.4/bin:$PATH"
    }
    stages{
        stage("Git Checkout"){
            steps{
                git credentialsId: 'd65219b8-656f-4ad1-832b-dfd5e9ec5e11', url: 'https://github.com/balu7632/my-app.git'
            }
        }
        stage("Maven Build"){
            steps{
                sh "mvn clean package"
                sh "mv target/*.war target/myweb.war"
            }
        }
        stage("build docker image"){
            steps{
                sh 'docker build -t balu7632/myweb .'
            }
        }
        stage('docker image push'){
            steps{
            withCredentials([string(credentialsId: 'docker-pwd', variable: 'DHPWD')]) {
                sh "docker login -u balu7632 -p ${DHPWD}"
        }
            sh "docker push balu7632/myweb"
            sh "docker rmi balu7632/myweb"
            }
        }
        stage("run container on dev server"){
            steps{
            "def" dockerRun = "docker run -itd -p 8081:8080 --name tomcat balu7632/myweb"
            sshagent(['tomcat-node-1']) {
              sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.34.70 ${dockerRun}"       
        }
            }
        }
    }
}    
