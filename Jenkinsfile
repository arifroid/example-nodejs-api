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

node ('nodejs') {
   stage ('Checkout'){
      git url: "${git_repo}", branch: "${git_branch}"
   }
 
   stage ('Prepare'){
      packageJson(host_db, port_db, name_db, user_db, pass_db)
      appName = sh( script: 'node -e "console.log(require(\'./package.json\').name);"', returnStdout: true).trim()
      appFullVersion = sh( script: 'node -e "console.log(require(\'./package.json\').version);"', returnStdout: true).trim()
      appFullVersion = appFullVersion.substring(0, appFullVersion.lastIndexOf('.')) + ".${BUILD_NUMBER}"
      gitCommitId = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
      appMajorVersion = appFullVersion.substring(0, appFullVersion.indexOf('.'))
      //sh 'sed -i \'s/ENVIRONMENT = "local"/ENVIRONMENT = "dev"/g\' ./src/config.js'
   }

    stage ('Docker Build'){
        sh """
        docker build  --rm -t https://hub.docker.com/repositories/arifroid/${appName}-v${appMajorVersion} .
      """
    }
    stage ('Docker Push'){
        sh """
        docker push https://hub.docker.com/repositories/arifroid/${appName}-v${appMajorVersion}
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
