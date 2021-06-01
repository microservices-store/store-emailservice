#!/usr/bin/env groovy

node {
	
    stage('Clone sources') {
        git url: 'git@github.com:ortelius/store-emailservice.git'
    }
    
    stage ('Testing') {

		// Update for your environment
		def DHURL="https://console.deployhub.com"
		def DHUSER="stella99"
		def DHPASS="123456"

		def COMPONENT_NAME="GLOBAL.Chasing Horses LLC.Vintage LLC.Hipster Store.Email Service.emailservice"
		def COMPONENT_VERSION="1.2.0"
		def COMPONENT_SERVICE_OWNER="Abraham Ortelius"
		def COMPONENT_SERVICE_OWNER_EMAIL="request-info@ortelius.io"
		def COMPONENT_SERVICE_OWNER_PHONE="505-559-4455"
		def COMPONENT_CUSTOMACTION="GLOBAL.HelmChart"
		def IMAGE_REGISTRY="quay.io/hipsterstore/emailservice"
		def IMAGE_TAG="latest"
		def HELM_CHART="chart/emailservice"
		def HELM_VERSION="1.0"
		def HELM_REPO=""
		def HELM_REPO_URL=""
		def HELM_NAMESPACE=""

		def APPLICATION_NAME="GLOBAL.Chasing Horses LLC.Vintage LLC..Hipster Store.Dev.Hipster Store;Labor Day Sale;1_2_15_0"

		// Derived values
        def GIT_BRANCH=eval2var('git ls-remote --heads origin | grep $(git rev-parse HEAD) | cut -d / -f 3').trim()
		def GIT_URL=eval2var('git config --get remote.origin.url').trim()       // remote url
		def GIT_REPO=eval2var('git config --local remote.origin.url | sed "s/[:\/]/\n/g" | tail -2 | tr "\n" "/" | sed "s/\.git\///"').trim()
		def GIT_COMMIT=eval2var('git log -1 --oneline | cut -f1 -d" "').trim()  // get latest commit on the branch
		def BLDDATE=eval2var('date').trim()
		def COMPONENT_VARIANT="${GIT_BRANCH}"
		def COMPONENT_VERSION_COMMIT="v${COMPONENT_VERSION}.${env.BUILD_NUMBER}-g${GIT_COMMIT}"

		// Override default tag name
   		IMAGE_TAG="${GIT_BRANCH}-v${COMPONENT_VERSION}.${env.BUILD_NUMBER}-g${GIT_COMMIT}"

		// Run Docker Build
		sh (returnStdout: true, script: "docker build -f Dockerfile --tag ${IMAGE_REGISTRY}:${IMAGE_TAG} . 2>&1")
		sh (returnStdout: true, script: "docker push ${IMAGE_REGISTRY}:${IMAGE_TAG} 2>&1")

		// Run Docker Push

		// Derive Disgest (must be done after push)
		def DIGEST=eval2var("docker inspect --format='{{index .RepoDigests 0}}' ${IMAGE_REGISTRY}:${IMAGE_TAG}")

		// Create component version and new application version in DeployHub
		sh "/usr/local/bin/dh updatecomp --dhurl ${DHURL} --dhuser ${DHUSER} --dhpass ${DHPASS} --appname '${APPLICATION_NAME}' --compname '${COMPONENT_NAME}' --compvariant '${COMPONENT_VARIANT}' --compversion '${COMPONENT_VERSION_COMMIT}' --compautoinc 'Y' --compattr GitCommit:${GIT_COMMIT}  --compattr GitUrl:${GIT_URL} --compattr 'GitRepo:${GIT_REPO}' --compattr 'GitBranch:${GIT_BRANCH}' --compattr BuildId:${env.BUILD_NUMBER} --compattr BuildUrl:${env.BUILD_URL} --compattr Chart:${HELM_CHART} --compattr ChartVersion:${HELM_VERSION} --compattr ChartNamespace:${HELM_NAMESPACE} --compattr ChartRepo:${HELM_REPO} --compattr ChartRepoUrl:${HELM_REPO_URL} --compattr 'DockerBuildDate:${BLDDATE}' --compattr DockerSha:${DIGEST} --compattr DockerRepo:${IMAGE_REGISTRY} --compattr DockerTag:${IMAGE_TAG} --compattr CustomAction:${COMPONENT_CUSTOMACTION} --compattr 'ServiceOwner:${COMPONENT_SERVICE_OWNER}' --compattr 'ServiceOwnerEmail:${COMPONENT_SERVICE_OWNER_EMAIL}' --compattr 'ServiceOwnerPhone:${COMPONENT_SERVICE_OWNER_PHONE}'"    
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