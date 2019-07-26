node {
    stage('SCM Checkout'){
        git credentialsId: 'RProjectPipeline', url: 'https://github.com/GilGonong/R-Project.git'
    }
    stage('Build'){
        bat 'docker build -t gvgonong/myshinyapp:3.0.1 .'
    }
    stage('Push to Container Registry'){
        withCredentials([string(credentialsId: 'dhpass', variable: 'dockerhubpass')]) {
            bat "docker login -u gvgonong -p ${dockerhubpass}"
        }
        bat 'docker push gvgonong/myshinyapp:3.0.1'
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
      slackSend baseUrl: 'https://hooks.slack.com/services/', channel: '#analytics', color: 'good', iconEmoji: '', message: 'Production deployment successful', teamDomain: 'MySlack', tokenCredentialId: 'slacking01', username: 'gilgonong'
    }
}   
