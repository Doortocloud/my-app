node {
    stage('SCM Checkout') {
        git 'https://github.com/Doortocloud/my-app.git'
        }
    stage('Maven Build') {
        def mvnHome = tool name: 'maven3', type: 'maven'
        sh "${mvnHome}/bin/mvn clean package"
            sh 'mv target/myweb*.war target/application.war'
        }
    stage('Build Docker Image') {
            sh "docker build -t premcloud/myweb:0.0.1 ."
            }
    stage('Docker Image Push') {
            withCredentials([string(credentialsId: 'Dockerpass', variable: 'dockerPassword')]) {
            sh "docker login -u premcloud -p ${dockerPassword}"
            }
                sh "docker push premcloud/myweb:0.0.1"
            }
    stage('Nexus image push'){
           withCredentials([usernamePassword(credentialsId: 'nexus-docker-creds', 
                                          usernameVariable: 'NEXUS_USER', 
                                          passwordVariable: 'NEXUS_PASS')]) {
            sh """
               docker login -u ${NEXUS_USER} -p ${NEXUS_PASS} 54.209.205.3:8082
               docker tag premcloud/myweb:0.0.1 54.209.205.3:8082/prem:1.0.0
               docker push 54.209.205.3:8082/prem:1.0.0
            """
        }
    }
    stage('Remove Previous Container'){
	try{
		sh 'docker rm -f myapplication'
	}catch(error){
		//  do nothing if there is an exception
	}
    stage('Docker deployment'){
   sh 'docker run -itd -p 8090:8080 --name myapplication premcloud/myweb:0.0.1' 
   }
}
}
