pipeline {


  environment {
    registry = "m912u/dvps"
    registryCredential = '204eb27a-2593-48d7-84cf-d6d98b6ba31f'
	dockerImage = ''
	appRepoUrl = 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
	appDockerfileRepoUrl = 'https://github.com/m912u/dz12_tomcatdockerfile.git'
  }


  agent {

    docker {
      image 'm912u/dvps:buildfarm'
	  args '-v /var/run/docker.sock:/var/run/docker.sock'
    }

  }

  stages {

    stage('Clone java app repo') {
      steps {
        git(url: appRepoUrl)
		}
    }

    stage('Build java app') {
      steps {
        sh 'mvn package'
		}
    }
	
    stage('Clone app dockerfile repo') {
		steps {
			git(url: appDockerfileRepoUrl)
		}
    }
	
    stage('Build app docker image') {
		steps {
			script {
				dockerImage = docker.build registry + ":$BUILD_NUMBER"
			}
		}
    }	
	
	stage('Push app docker image to registry') {
		steps{   	
			script {
				docker.withRegistry( '', registryCredential ) {
					dockerImage.push()
				}
			}
		}
	}
	
	stage('Run app on node') {
		steps{   
		  sshagent (credentials: ['389f5597-193c-49c6-b218-d9eeac52bb46']) {
			sh 'ssh -t root@84.201.136.127 docker run -p 8081:8080 -d ' + registry + ":$BUILD_NUMBER"
	}
		}
	}
  }
}
