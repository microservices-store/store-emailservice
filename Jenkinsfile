#!/usr/bin/env groovy

node {
	
    stage('Clone sources') {
        git url: 'git@github.com:ortelius/store-emailservice.git'
    }
    
    stage ('Testing') {

		// Update for your environment
		def DHUrl="https://console.deployhub.com"
		def DHUsername="stella99"
		def DHPassword="123456"

		def CompName="GLOBAL.Santa Fe Software.Online Store Company.Store Services.Email Service.emailservice"
		def CompVersion="1.2.0"
		def ServiceOwner="Abraham Ortelius"
		def ServiceOwnerEmail="request-info@ortelius.io"
		def ServiceOwnerPhone="505-559-4455"
		def CustomAction="GLOBAL.HelmChart"
		def ImageRegistry="quay.io/hipsterstore/emailservice"
		def ImageTag="latest"
		def HelmChart="chart/emailservice"
		def HelmChartVersion="1.0"
		def HelmRepo=""
		def HelmRepoUrl=""
		def HelmNamespace=""

		def AppName="GLOBAL.Santa Fe Software.Online Store Company.Candy Store"
		def AppVersion="v1.0.0"

		// Derived values
        def GitBranch=eval2var('git ls-remote --heads origin | grep $(git rev-parse HEAD) | cut -d / -f 3').trim()
		def GitUrl=eval2var('git config --get remote.origin.url').trim()       // remote url
		def GitRepo=eval2var('git config --local remote.origin.url | sed "s/[:\\/]/\\n/g" | tail -2 | tr "\\n" "/" | sed "s/\\.git\\///"').trim()
		def GitCommit=eval2var('git log -1 --oneline | cut -f1 -d" "').trim()  // get latest commit on the branch
		def BuildDate=eval2var('date').trim()
		def CompVariant="${GitBranch}"
		def CompVersionCommit="v${CompVersion}.${env.BUILD_NUMBER}-g${GitCommit}"
		def CompReadme=eval2var("ls README* 1>/dev/null 2> /dev/null && echo ${GitUrl}#readme")

		// Override default tag name
   		ImageTag="${GitBranch}-v${CompVersion}.${env.BUILD_NUMBER}-g${GitCommit}"

		// Run Docker Build
		sh (returnStdout: true, script: "docker build -f Dockerfile --tag ${ImageRegistry}:${ImageTag} . 2>&1")
		sh (returnStdout: true, script: "docker push ${ImageRegistry}:${ImageTag} 2>&1")

		// Run Docker Push

		// Derive Disgest (must be done after push)
		def ImageDigest=eval2var("docker inspect --format='{{index .RepoDigests 0}}' ${ImageRegistry}:${ImageTag} | tr -d '\\n'").tokenize(':')[1]

		// Create component version and new application version in DeployHub
		sh "/usr/local/bin/dh updatecomp --dhurl '${DHUrl}' --dhuser '${DHUsername}' --dhpass '${DHPassword}' --appname '${AppName}' --appversion '${AppVersion}' --appautoinc 'Y' --compname '${CompName}' --compvariant '${CompVariant}' --compversion '${CompVersionCommit}' --compattr 'GitCommit:${GitCommit}'  --compattr 'GitUrl:${GitUrl}' --compattr 'GitRepo:${GitRepo}' --compattr 'GitBranch:${GitBranch}' --compattr 'BuildId:${env.BUILD_NUMBER}' --compattr 'BuildUrl:${env.BUILD_URL}' --compattr 'Chart:${HelmChart}' --compattr 'ChartVersion:${HelmChartVersion}' --compattr 'ChartNamespace:${HelmNamespace}' --compattr 'ChartRepo:${HelmRepo}' --compattr 'ChartRepoUrl:${HelmRepoUrl}' --compattr 'DockerBuildDate:${BuildDate}' --compattr 'DockerSha:${ImageDigest}' --compattr 'DockerRepo:${ImageRegistry}' --compattr 'DockerTag:${ImageTag}' --compattr 'CustomAction:${CustomAction}' --compattr 'ServiceOwner:${ServiceOwner}' --compattr 'ServiceOwnerEmail:${ServiceOwnerEmail}' --compattr 'ServiceOwnerPhone:${ServiceOwnerPhone}' --compattr 'Readme:${CompReadme}'"    
    }  
}


// Function to disable shell echo and grab output from command
def eval2var(script) {
    if (isUnix()) {
        return sh(returnStdout: true, script: '#!/bin/sh -e\n' + script);
    } else {
        return bat(returnStdout: true, script: '#!/bin/sh -e\n' + script);
    }
}	