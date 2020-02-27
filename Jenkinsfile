@Library('defra-library@0.0.13')
import uk.gov.defra.ffc.DefraUtils
def defraUtils = new DefraUtils()

// Versioning - edit these variables to set version information
def dockerfileVersion = '1.0.0'
def dotNetVersion = '12.16.0'

// Constants
def awsRegion = 'eu-west-2'
def imageNameProduction = 'ffc-dotnetcore'
def imageNameDevelopment = 'ffc-dotnetcore-development'
def regCredsId = 'ecr:eu-west-2:ecr-user'
def registry = '562955126301.dkr.ecr.eu-west-2.amazonaws.com'
def repoName = 'ffc-docker-dotnetcore'

// Variables
def containerTag = ''
def imageTag = ''
def mergedPrNo = ''
def pr = ''

node {
  checkout scm

  try {
    stage('Set variables') {
      imageTag = "$dockerfileVersion-dotnet${dotNetVersion}"
      (pr, containerTag, mergedPrNo) = defraUtils.getVariables(repoName, imageTag)
      defraUtils.setGithubStatusPending()

      imageRepositoryProduction = "$registry/$imageNameProduction"
      imageRepositoryDevelopment = "$registry/$imageNameDevelopment"
      imageTag = imageTag + (pr ? "-pr$pr" : "")
    }

    stage('Build') {
      sh "docker build --no-cache --tag $imageRepositoryProduction:$imageTag --build-arg DOTNET_VERSION=${dotNetVersion} \
      --build-arg VERSION=$dockerfileVersion ./runtime"

      sh "docker build --no-cache --tag $imageRepositoryDevelopment:$imageTag --build-arg DOTNET_VERSION=${dotNetVersion} \
      --build-arg VERSION=$dockerfileVersion ./sdk"
    }

    stage('Push') {
      docker.withRegistry("https://$registry", regCredsId) {
        sh "docker push $imageRepositoryProduction:$imageTag"
        sh "docker push $imageRepositoryDevelopment:$imageTag"
      }
    }

    if (mergedPrNo) {
      // Remove PR image tags from registry after merge to master.
      // Leave digests as these will be reused by master build or cleaned up automatically.
      prImageTag = "$dockerfileVersion-dotnet${dotNetVersion}-$mergedPrNo"
      stage('Clean registry') {
        sh """
          aws --region $awsRegion \
            ecr batch-delete-image \
            --image-ids imageTag=$prImageTag \
            --repository-name $imageNameProduction
        """
        sh """
          aws --region $awsRegion \
            ecr batch-delete-image \
            --image-ids imageTag=$prImageTag \
            --repository-name $imageNameDevelopment
        """
      }
      stage('Tag release in github') {
        withCredentials([
          string(credentialsId: 'github_ffc_platform_repo', variable: 'gitToken')
        ]) {
          defraUtils.triggerRelease(imageTag, repoName, imageTag, gitToken)
        }
      }
    }

    defraUtils.setGithubStatusSuccess()
  } catch(e) {
    defraUtils.setGithubStatusFailure(e.message)
    throw e
  }
}
