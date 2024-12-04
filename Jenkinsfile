pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        SONARQUBE = 'http://172.31.18.183:9000'
    }
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/Brainace77/jenkins-eks.git'
                }
        }

        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }

       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
	stage("SonarQube Analysis") {
	    steps {
	        script {
	            withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
	                sh "mvn clean install sonar:sonar -Dsonar.host.url=$SONARQUBE"
	            }
	        }
	    }
	}

	
	stage("Quality Gate") {
	    steps {
	        script {
	            echo "Waiting for SonarQube Quality Gate..."
	            def qg = waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
	            if (qg.status != 'OK') {
	                error "SonarQube Quality Gate failed: ${qg.status}"
	            } else {
	                echo "SonarQube Quality Gate passed!"
	            }
	        }
	    }
	}

   }
}
