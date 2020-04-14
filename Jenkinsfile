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
/**	
        stage ('Testing Stage'){
        def mvnHome = tool name: 'apache-maven-3.6.1', type: 'maven'
        def mvnCMD = "${mvnHome}/bin/mvn"
        sh "${mvnCMD} test"
        }
*/        
        stage('Build Docker Image'){
            sh 'docker build -t pannly/my-app:2.0.0 .'

// Send slack message		
	currentBuild.result = "Successed"
	message = """
        *Jenkins Build*
        Job name: `${env.JOB_NAME}`
        Build number: `#${env.BUILD_NUMBER}`
        Build status: `${currentBuild.result}`
        Build details: <${env.BUILD_URL}/console|See in web console>
    """.stripIndent()
	slackSend(color: 'good', message: message)
        }
 
        stage('Remove Previous Container'){
        	try{
        	    sh 'docker rm -f my-app'
		slackSend(color: 'good', message: Removed my-app container !! )
        	}catch(error){
		//  do nothing if there is an exception
		slackSend(color: 'good', message: No running my-app container !! )
	        }
        }
        
        stage('Run Docker Container'){
            sh 'docker run -p 8888:8080 --rm --name my-app pannly/my-app:2.0.0; sleep 1'
        }

        stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'dockerp', variable: 'dockerp')]) {
          sh "docker login -u pannly -p ${dockerp}"
        }
          sh 'docker push pannly/my-app:2.0.0'
	  slackSend(color: 'good', message: Push image to docker repo success !! )	
     }

    }
