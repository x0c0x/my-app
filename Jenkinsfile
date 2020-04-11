node{
        stage('SCM Checkout'){
        git credentialsId: 'git-creds', url: 'https://github.com/x0c0x/my-app.git'    
        }
        
        stage ('Build mvn Packages'){
        def mvnHome = tool name: 'apache-maven-3.6.1', type: 'maven'
        def mvnCMD = "${mvnHome}/bin/mvn"
        sh "${mvnCMD} clean package"
        }
        
	stage('SonarQube Analysis') {
        def mvnHome =  tool name: 'apache-maven-3.6.1', type: 'maven'
        withSonarQubeEnv('sonar7') { 
          sh "${mvnHome}/bin/mvn sonar:sonar"
        	}
   	}	
	
        stage ('Testing Stage'){
        def mvnHome = tool name: 'apache-maven-3.6.1', type: 'maven'
        def mvnCMD = "${mvnHome}/bin/mvn"
        sh "${mvnCMD} test"
        }
        
        stage('Build Docker Image'){
            sh 'docker build -t pannly/my-app:2.0.0 .'
        }
 
        stage('Remove Previous Container'){
        	try{
        	    sh 'docker rm -f my-app'
        	}catch(error){
		//  do nothing if there is an exception
	        }
        }
        
        stage('Run Docker Container'){
            sh 'docker run -p 8888:8080 -d --name my-app pannly/my-app:2.0.0'
        }

        stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'dockerp', variable: 'dockerp')]) {
          sh "docker login -u pannly -p ${dockerp}"
        }
          sh 'docker push pannly/my-app:2.0.0'
     }

    }
