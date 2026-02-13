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
        bat """
        docker build  --rm -t arifroid/qiscus .
      """
    }
    stage ('Docker Push'){
        withCredentials([usernamePassword(
            credentialsId: '50d75c2d-a56e-43f5-919a-551e987448e4',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
        bat """
        docker login -u %DOCKER_USER% -p %DOCKER_PASS%
        docker push arifroid/qiscus:latest
      """
        }
    }
    
    stage('Update image & restart service') {
    sshagent(['qiscus']) {
        bat """
        ssh -o StrictHostKeyChecking=no ubuntu@3.234.193.168 << 'EOF'
        sudo docker pull arifroid/qiscus:latest
        sudo docker rm -f api-nodejs
        sudo docker run -d -p 3000:3000 --name api-nodejs arifroid/qiscus:latest
        EOF
        """
    }
}

}
