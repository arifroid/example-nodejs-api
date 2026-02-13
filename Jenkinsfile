def buildNumber = env.BUILD_NUMBER as int
if (buildNumber > 1) milestone(buildNumber - 1)
milestone(buildNumber)


def micrositeName = ''
def git_repo = 'https://code.mylabzolution.com/marketplace/diskon-management-service.git'
def public_route_prefix = 'marketplace'

def git_branch= 'dev'

def nexus_base_url = 'https://library.mylabzolution.com'
def nexus_deploy_repo = "$nexus_base_url/repository/ist_npm/"

def cpu_req = '30m'
def memory_req = '200Mi'
def cpu_limit = '50m'
def memory_limit = '300Mi'
def max_replica_count = 1

def env = 'market-prod'
def pull_secret = 'default-dockercfg-p57zk'

def appName
def appFullVersion
def gitCommitId
    
def host_db = '10.184.0.4'
def port_db = '5435'
def name_db = 'marketplace'
def user_db = 'diskon_management_service'
def pass_db = 'Sv%bh6ZT=k7M<)qpQn}-S&xjC=7'

node ('nodejs') {
   stage ('Checkout'){
      git url: "${git_repo}", branch: "${git_branch}", credentialsId: 'gagah'
   }
 
   stage ('Prepare'){
      withCredentials([[$class: 'UsernamePasswordMultiBinding',
         credentialsId: 'nexus',
         usernameVariable: 'nexus_username', passwordVariable: 'nexus_password']]) {
               sh """
                  echo 'Downloading ci-cd templates...'
                  rm -rf ./cicd-template
                  curl --fail -u ${nexus_username}:${nexus_password} -o cicd-template-${env}.tar.gz ${nexus_base_url}/repository/general-ist/cicd-template-${env}.tar.gz
                  mkdir cicd-template && tar -xzvf ./cicd-template-${env}.tar.gz -C "\$(pwd)/cicd-template"
                  """
      }
      
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
        oc new-app image.mylab-siab.com/library/${appName}-v${appMajorVersion} --name ${appName}-v${appMajorVersion} -n ${ocp_project} || echo 'app exists'
      """
    }
    
    stage ('Update image'){
        sh """
        oc import-image image.mylab-siab.com/library/${appName}-v${appMajorVersion}:latest --confirm -n ${ocp_project}
      """
    }
}

    def createRoute(String name) {
        try {
            def service = getServiceName(name)
            sh "oc expose svc ${service}"
        } catch(Exception e) {
            echo "route exists"
        }    
    }
    def getServiceName(String name) {
        def cmd4 = $/service=$(oc get svc -l app=${name} -o name);echo $${service##service*/}/$
        svc = sh(returnStdout: true, script: cmd4).trim()        
        return svc
    }

    def packageJson(host_db, port_db, name_db, user_db, pass_db){
        paramfile = readFile('./package.json')
        paramfile = paramfile.replaceAll('\\$host_db', host_db)
        paramfile = paramfile.replaceAll('\\$port_db', port_db)
        paramfile = paramfile.replaceAll('\\$name_db', name_db)
        paramfile = paramfile.replaceAll('\\$user_db', user_db)
        paramfile = paramfile.replaceAll('\\$pass_db', pass_db)

        writeFile file: './package.json', text: paramfile

    }
        ]
