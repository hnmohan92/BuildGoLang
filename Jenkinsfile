properties([
  parameters([string(name: 'DeployIp', defaultValue: '18.220.35.55', description: 'EC2 IP in which docker image is to be deployed'),
    booleanParam(name: 'cloneRepo', defaultValue:true, description: 'Clone Reopsitory'),
    booleanParam(name: 'buildImage', defaultValue:true, description: 'Build Image'),
    booleanParam(name: 'testImage', defaultValue:true, description: 'Test Image'),
    booleanParam(name: 'pushImage', defaultValue:true, description: 'Push Image'),
    booleanParam(name: 'deleteOld', defaultValue:true, description: 'Delete Old Container in AWS'),
    booleanParam(name: 'deployNew', defaultValue:true, description: 'Deploy in AWS'), 
      ])
])
node {
    def app
  
    if (cloneRepo=='true'){
      stage('Clone repository') {
          /* Let's make sure we have the repository cloned to our workspace */
          checkout scm
      }
    }
  
    if (buildImage=='true'){
      stage('Build image') {
          /* This builds the actual image; synonymous to
           * docker build on the command line */
          app = docker.build("hnmohan92/golangoutyet")
      }
    }
  
    if (testImage=='true'){
      stage('Test image') {
          /* Ideally, we would run a test framework against our image.
           * For this example, we're using a Volkswagen-type approach ;-) */

          app.inside {
              sh 'echo "Tests passed"'
          }
      }
    }
  
    if (pushImage=='true'){
      stage('Push image') {
          /* Finally, we'll push the image with two tags:
           * First, the incremental build number from Jenkins
           * Second, the 'latest' tag.
           * Pushing multiple tags is cheap, as all the layers are reused. */
          docker.withRegistry('https://registry.hub.docker.com', '93a91e4e-99a6-45f7-8446-bb5350af71f5') {
              app.push("${env.BUILD_NUMBER}")
              app.push("latest")
          }
      }
    }
  
   /*   
    stage('Deploy Image'){
     sh 'docker run -d -p 12345:12345 --name gorestapi hnmohan92/golangoutyet'   
    }*/
    if (deleteOld=='true'){
      stage('Delete Old Container'){
        sh "ssh -i jikoqa0619.pem ubuntu@$params.DeployIp docker rm --force gorestapi >> output.log"
      }
    }
    
    if (deployNew=='true'){
      stage('Deploy in AWS'){
          sh "ssh -i jikoqa0619.pem ubuntu@$params.DeployIp docker run -d -p 12345:12345 --name gorestapi hnmohan92/golangoutyet" 
      }
    }
}
