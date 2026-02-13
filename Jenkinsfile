def buildNumber = env.BUILD_NUMBER as int
if (buildNumber > 1) milestone(buildNumber - 1)
milestone(buildNumber)


def micrositeName = ''
def git_repo = 'https://github.com/arifroid/example-nodejs-api.git'
def public_route_prefix = 'marketplace'

def git_branch= 'main'

def cpu_req = '30m'
def memory_req = '200Mi'
def cpu_limit = '50m'
def memory_limit = '300Mi'
def max_replica_count = 1

def appName
def appFullVersion
def gitCommitId

node ('master') {
   stage ('Checkout'){
      git url: "${git_repo}", branch: "${git_branch}"
   }

    stage ('Docker Build'){
        sh """
        docker build  --rm -t https://hub.docker.com/repositories/arifroid/example-nodejs-api .
      """
    }
    stage ('Docker Push'){
        sh """
        docker push https://hub.docker.com/repositories/arifroid/example-nodejs-api
      """
    }

    stage ('OpenShift Create App'){
        sh """
        
      """
    }
    
    stage ('Update image'){
        sh """
        
      """
    }
}
