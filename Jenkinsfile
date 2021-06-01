#!/usr/bin/env groovy

node {
	
    stage('Clone sources') {
        git url: 'git@github.com:ortelius/store-emailservice.git'
    }
    
    stage ('Testing') {
		/*********************************/
		/*   Update for your component   */
		/*********************************/
		def DHUrl="https://console.deployhub.com"
		def DHUsername="stella99"
		def DHPassword="123456"

		def AppName="GLOBAL.Santa Fe Software.Online Store Company.Candy Store"
		def AppVersion="v1.0.0"

		def CompName="GLOBAL.Santa Fe Software.Online Store Company.Store Services.Email Service.emailservice"
		def CompVersion="1.2.0"

		def CustomAction="GLOBAL.HelmChart"

		def HelmChart="chart/emailservice"
		def HelmChartVersion="1.0"
		def HelmNamespace=""
		def HelmRepo=""
		def HelmRepoUrl=""

		def ImageRegistry="quay.io/hipsterstore/emailservice"
		def ImageTag="latest"

		def ServiceOwner="Abraham Ortelius"
		def ServiceOwnerEmail="request-info@ortelius.io"
		def ServiceOwnerPhone="505-559-4455"

		/*********************************/
		/*        Derived Values         */
		/*********************************/
        def GitBranch=eval2var('git ls-remote --heads origin | grep $(git rev-parse HEAD) | cut -d / -f 3').trim()
		def GitUrl=eval2var('git config --get remote.origin.url').trim()       // remote url
		def GitRepo=eval2var('git config --local remote.origin.url | sed "s/[:\\/]/\\n/g" | tail -2 | tr "\\n" "/" | sed "s/\\.git\\///"').trim()
		def GitCommit=eval2var('git log -1 --oneline | cut -f1 -d" "').trim()  // get latest commit on the branch
		def BuildDate=eval2var('date').trim()
		def BuildId=${env.BUILD_NUMBER}
		def BuildUrl=${env.BUILD_URL}
		def CompVariant="${GitBranch}"
		def CompVersionCommit="v${CompVersion}.${env.BUILD_NUMBER}-g${GitCommit}"
		def CompReadme=eval2var('ls README* 1>/dev/null 2> /dev/null && echo "${GitUrl}#readme" || echo ""')

		/*********************************/
		/* Override Default Tag Name     */
		/*********************************/
   		ImageTag="${GitBranch}-v${CompVersion}.${env.BUILD_NUMBER}-g${GitCommit}"

  
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