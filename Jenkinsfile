pipeline {
    agent any
    triggers { pollSCM('H/5 * * * *') }
    stages {
        stage('Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "./mvnw clean package"
            }
        }
    }
	post {
		// If Maven was able to run the tests, even if some of the test
		// failed, record the test results and archive the jar file.
		always {
			junit '**/target/surefire-reports/TEST-*.xml'
			archiveArtifacts 'target/*.jar'
		}
		changed {
			emailext attachLog: true, 
			body: "Please go to ${BUILD_URL} and verify the build", 
			compressLog: true, 
			recipientProviders: [upstreamDevelopers(), requestor()], 
			subject: "Job \'${JOB_NAME}\' (build ${BUILD_NUMBER}) ${currentBuild.result}",
			to: "test@jenkins.com"
		}
	}
}
