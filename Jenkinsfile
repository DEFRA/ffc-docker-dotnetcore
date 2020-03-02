@Library('defra-library@0.0.13')
import uk.gov.defra.ffc.DefraUtils
def defraUtils = new DefraUtils()

// Versioning - edit these variables to set version information
def dockerfileVersion = '1.0.0'
def dotNetVersion = '12.16.0'

// Constants
def awsCredential = 'devffc-user'
def awsRegion = 'eu-west-2'
def imageNameProduction = 'ffc-dotnetcore'
def imageNameDevelopment = 'ffc-dotnetcore-development'
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

      imageRepositoryDevelopment = "$DOCKER_REGISTRY/$imageNameDevelopment"
      imageRepositoryProduction = "$DOCKER_REGISTRY/$imageNameProduction"
    }

    stage('Build') {
      sh "docker build --no-cache --tag $imageRepositoryDevelopment:$imageTag --build-arg DOTNET_VERSION=${dotNetVersion} \
      --build-arg VERSION=$dockerfileVersion ./sdk"

      sh "docker build --no-cache --tag $imageRepositoryProduction:$imageTag --build-arg DOTNET_VERSION=${dotNetVersion} \
      --build-arg VERSION=$dockerfileVersion ./runtime"
    }

    stage('Push') {
      docker.withRegistry("https://$DOCKER_REGISTRY", DOCKER_REGISTRY_CREDENTIALS_ID) {
        sh "docker push $imageRepositoryDevelopment:$imageTag"
        sh "docker push $imageRepositoryProduction:$imageTag"
      }
    }

    if (mergedPrImageTag) {
      // Remove PR image tags from registry after merge to master.
      // Leave digests as these will be reused by master build or cleaned up automatically.
      stage('Clean registry') {
        withAWS(credentials: awsCredential, region: 'eu-west-2') {
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
