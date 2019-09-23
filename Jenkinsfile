node{
	tool name: 'Gradle', type: 'gradle'
	checkout([$class: 'GitSCM', branches: [[name: '*/asilnikov']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/asilnikov/Pipeline.git']]])

stage('Preparation (Checking out)') { 
	git([url: 'https://github.com/asilnikov/Pipeline.git', branch: "asilnikov"])
	echo 'Checking out: COMPLETED SUCCESSFULLY!'
	}

stage('Building code') {
	"Gradle build" 
	echo 'building code: COMPLETED SUCCESSFULLY!'
	}
    
stage('Testing') {
parallel (
    'Cucumber Tests': { "Gradle cucumber --status" },
    'Jacoco Tests': { "Gradle jacocoTestReport --status" },
    'Unit Tests': { "Gradle test --status" }
    )
    echo 'tests: COMPLETED SUCCESSFULLY!'
	}

stage ('Triggering job and fetching artefact after finishing') {
build job: "MNTLAB-asilnikov-child1-build-job", parameters: [string(name: 'BRANCH_NAME', value: 'asilnikov')]

step(
    [$class: 'CopyArtifact',
    filter: "asilnikov_dsl_script.tar.gz",
    projectName: "MNTLAB-asilnikov-child1-build-job" ])
    echo 'build job: COMPLETED SUCCESSFULLY!'
	}
stage('Packaging and Publishing results') {
            sh ("tar -xzf asilnikov_dsl_script.tar.gz && cp build/libs/*.jar ./")
            archiveArtifacts artifacts: 'gradle-simple.jar, Jenkinsfile, jobs.groovy', onlyIfSuccessful: true
            echo 'Packaging and Publishing: COMPLETED SUCCESSFULLY!'
    }

stage ('Asking for manual approval') {
   timeout(time:1, unit:'HOURS') {
	input message:'Please approve deployment', ok: 'APPROVE'
	 }
	}

stage ('Deployment') {
	sh 'java -jar build/libs/gradle-simple.jar'
	echo 'Deployment: COMPLETED SUCCESSFULLY!'
	}

stage ('Sending status') {
	echo 'Pipeline: COMPLETED SUCCESSFULLY!'
	}
}
