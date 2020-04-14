node{
        stage('SCM Checkout'){
        git credentialsId: 'git-creds', url: 'https://github.com/x0c0x/my-app.git'    
        }
        
        stage ('Build mvn Packages'){
        def mvnHome = tool name: 'apache-maven-3.6.1', type: 'maven'
        def mvnCMD = "${mvnHome}/bin/mvn"
        slackSend channel: '#jenkins-build', color: 'good', message: "Job -  *${env.JOB_NAME}*, Build package is starting ....."
	sh "${mvnCMD} clean package"
	currentBuild.result = 'SUCCESS'
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
            slackSend channel: '#jenkins-build', color: 'good', message: "Job -  *${env.JOB_NAME}*, Docker image is building....."
	    sh 'docker build -t pannly/my-app:2.0.0 .'
	}
// Send slack message		
/*	
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
		slackSend channel: '#jenkins-build', color: 'good', message: "Job -  ${env.JOB_NAME}, Removed my-app container !!"
        	}catch(error){
		//  do nothing if there is an exception
		slackSend channel: '#jenkins-build', color: 'danger', message: "Job -  ${env.JOB_NAME}, No running my-app container !!"
	        }
        }
*/      
        stage('Test Run Container'){
            sh 'docker run -p 8888:8080 -d --name my-app pannly/my-app:2.0.0'
	    sh 'docker stop my-app'
	    sh 'docker rm my-app'
        }

        stage('Push docker image to DockerHub '){
          withCredentials([string(credentialsId: 'dockerp', variable: 'dockerp')]) {
          sh "docker login -u pannly -p ${dockerp}"
	  sh 'docker push pannly/my-app:2.0.0'
        }  
//	  slackSend channel: '#jenkins-build', color: 'good', message: "Job -  ${env.JOB_NAME}, Completed successfully Build URL is ${env.BUILD_URL}"	
//     	currentBuild.result = 'SUCCESS'
	message = """
        *Jenkins Build*
        Job name: `${env.JOB_NAME}`
        Build number: `#${env.BUILD_NUMBER}`
        Build status: `${currentBuild.result}`
        Build details: <${env.BUILD_URL}/console|See in web console>
    """.stripIndent()
	slackSend(color: 'good', message: message)
	}
    }
