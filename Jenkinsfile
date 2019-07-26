node {
    stage('SCM Checkout'){
        git credentialsId: 'RProjectPipeline', url: 'https://github.com/GilGonong/R-Project.git'
    }
    stage('Build'){
        bat 'docker build -t gvgonong/myshinyapp:1.0.0 .'
    }
    stage('Push to Container Registry'){
        withCredentials([string(credentialsId: 'docker-pwd', variable: 'docker-pwd')]) {
            bat "docker login -u gvgonong -p ${docker-pwd}"
        }
        bat 'docker push gvgonong/myshinyapp:1.0.0'
    }
    stage('Stage'){
      #add stage server connection here
      def dockerRun = 'docker run -p 8080:8080 -d --name myshinyapp gvgonong/myshinyapp'
      sshagent(['']){
        bat "ssh -o StrictHostKeyChecking=no username@ipaddress ${dockerRun}"
      }
    }
    stage('Production'){
      def dockerRun = 'docker run -p 8080:8080 -d --name myshinyapp gvgonong/myshinyapp'
      sshagent(['']){
        bat "ssh -o StrictHostKeyChecking=no username@ipaddress ${dockerRun}"
    }
    stage('Send notification'){
      slackSend channel: '#analytics', color: 'good', iconEmoji: '', message: 'Production Deployment Succesfull', teamDomain: 'https://hooks.slack.com/services/', tokenCredentialId: 'slackIngtegration', username: 'GilGonong'
    }
}   
