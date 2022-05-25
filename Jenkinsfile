pipeline {

	// More info about the Declarative Pipeline Syntax on https://www.jfrog.com/confluence/display/JFROG/Declarative+Pipeline+Syntax
	// Declarative syntax is available from version 3.0.0 of the Jenkins Artifactory Plugin.

	agent any

	

	
	stages {
		
		stage ("Clone") {
			steps {
				git branch: "main",
				url: "https://github.com/MaharshiPatel/nodeJs-sample-project"
				// credentialsId: 'git_cred_id'. If cloning the code requires credentials, set the credentials to your git in Jenkins > Configure System > credentials > "username with password" > ID: "git-cred-id"
			}
		}

		stage ("Artifactory configuration") {
			steps {
				rtServer (
					id: "jfrog-instance",
					url: "https://soleng.jfrog.io/artifactory/",
					credentialsId: 'rt-cred-id', // Set the credentials to your JFrog instance in Jenkins > Configure System > credentials > "username with password" > ID: "rt-cred-id"

 					// bypassProxy: true, (If Jenkins is configured to use an http proxy, you can bypass the proxy when using this Artifactory server)
					// timeout: 300 , (Configure the connection timeout (in seconds). The default value (if not configured) is 300 seconds)
				)
				rtNpmDeployer (
					id: "NPM_DEPLOYER",
					serverId: "jfrog-instance",
					repo: "sup016-npm-dev-local",

					// threads: 6, (Optional - Attach custom properties to the published artifacts)
					// properties: ['key1=value1', 'key2=value2'], (Optional - Attach custom properties to the published artifacts)
				)
				rtNpmResolver (
					id: "NPM_RESOLVER",
					serverId: "jfrog-instance",
					repo: "sup016-npm-virtual"
				)
			}
		}

		stage ("Exec Npm install") {
			steps {
				rtNpmInstall (
					resolverId: "NPM_RESOLVER",

					// tool: {build installation name}, (Npm tool installation from jenkins from : Jenkins > Manage jenkins > Global Tool Configuration > NodeJS installations
				)
			}
		}

		stage ("Exec Npm publish") {
			steps {
				rtNpmPublish (
					deployerId: "NPM_DEPLOYER",

					// tool: {build installation name}, (Npm tool installation from jenkins from : Jenkins > Manage jenkins > Global Tool Configuration > NodeJS installations
					// path: '',  (Optional path to the project root. If not set, the root of the workspace is assumed as the root project path.)
					// javaArgs: '-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005' , (Jenkins spawns a new java process during this step's execution. You have the option of passing any java args to this new process.)
				)
			}
		}

		stage ("Publish build info") {
			steps {
				rtPublishBuildInfo (
					serverId: "jfrog-instance",
				)
			}
		}

	}
}