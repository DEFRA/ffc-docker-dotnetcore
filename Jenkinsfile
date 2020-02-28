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
def registry = '562955126301.dkr.ecr.eu-west-2.amazonaws.com'
def repoName = 'ffc-docker-dotnetcore'

// Variables
def imageTag = ''
def imageRepositoryDevelopment = ''
def imageRepositoryProduction = ''
def mergedPrImageTag = ''
def pr = ''
def versionTag = ''

node {
  checkout scm

  try {
    stage('Set variables') {
      versionTag = "${dockerfileVersion}-dotnet${dotNetVersion}"
      (pr, imageTag, mergedPrImageTag) = defraUtils.getVariables(repoName, versionTag)
      defraUtils.setGithubStatusPending()

      imageRepositoryDevelopment = "$registry/$imageNameDevelopment"
      imageRepositoryProduction = "$registry/$imageNameProduction"
    }

    stage('Build') {
      sh "docker build --no-cache --tag $imageRepositoryDevelopment:$imageTag --build-arg DOTNET_VERSION=${dotNetVersion} \
      --build-arg VERSION=$dockerfileVersion ./sdk"

      sh "docker build --no-cache --tag $imageRepositoryProduction:$imageTag --build-arg DOTNET_VERSION=${dotNetVersion} \
      --build-arg VERSION=$dockerfileVersion ./runtime"
    }

    stage('Push') {
      docker.withRegistry("https://$registry") {
        sh "docker push $imageRepositoryDevelopment:$imageTag"
        sh "docker push $imageRepositoryProduction:$imageTag"
      }
    }

    if (mergedPrImageTag) {
      // Remove PR image tags from registry after merge to master.
      // Leave digests as these will be reused by master build or cleaned up automatically.
      stage('Clean registry') {
        sh """
          aws --region $awsRegion \
            ecr batch-delete-image \
            --image-ids imageTag=$mergedPrImageTag \
            --repository-name $imageNameDevelopment
        """
        sh """
          aws --region $awsRegion \
            ecr batch-delete-image \
            --image-ids imageTag=$mergedPrImageTag \
            --repository-name $imageNameProduction
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
