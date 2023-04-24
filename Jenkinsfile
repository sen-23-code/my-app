node{
   stage('SCM Checkout'){
     git 'https://github.com/balajihooghli/my-app.git/'
   }
   stage('Compile-Package'){
     def mvnHome =  tool name: 'maven3', type: 'maven'   
     sh "${mvnHome}/bin/mvn clean package"
     sh 'mv target/myweb*.war target/newapp.war'
}
   stage('Build Docker Imager'){
     sh 'docker build -t balajihooghli/myweb:0.0.11 .'
   }
   stage('SonarQube Analysis') {
	 def mvnHome =  tool name: 'maven3', type: 'maven'
	 withSonarQubeEnv('sonar') 
	 { 
	 sh "${mvnHome}/bin/mvn sonar:sonar"
     }
    }
   stage('Docker Image Push'){
     withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
     sh "docker login -u balajihooghli -p ${dockerPassword}"
    }
    sh 'docker push balajihooghli/myweb:0.0.11'
   }
   stage('Nexus Image Push'){
     withCredentials([string(credentialsId: 'nexus', variable: 'nexusPassword')]) 
     {
     sh "docker login -u admin -p ${nexusPassword} 13.127.157.219:8083"
     sh "docker tag balajihooghli/myweb:0.0.11 13.127.157.219:8083/balhooghsonarbh:11.0.0"
     sh 'docker push 13.127.157.219:8083/balhooghsonarbh:11.0.0'
   }
   }
   stage('Remove Previous Container'){
	 try{
	    sh 'docker rm -f tomcattestbh'
	    }
	 catch(error){
		//  do nothing if there is an exception
	}
   }
    stage('Docker deployment'){
     sh 'docker run -d -p 8090:8080 --name tomcattestbh balajihooghli/myweb:0.0.11' 
   }

}
