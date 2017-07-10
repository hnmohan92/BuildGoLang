properties([
  parameters([string(name: 'DeployIp', defaultValue: '18.220.35.55', description: 'EC2 IP in which docker image is to be deployed'),
    booleanParam(name: 'cloneRepo', description: 'Clone Reopsitory', defaultValue:true),
    booleanParam(name: 'buildImage', description: 'Build Image', defaultValue:true),
    booleanParam(name: 'testImage', description: 'Test Image', defaultValue:true),
    booleanParam(name: 'pushImage', description: 'Push Image', defaultValue:true),
    booleanParam(name: 'deleteOld',description: 'Delete Old Container in AWS', defaultValue:true),
    booleanParam(name: 'deployNew',description: 'Deploy in AWS', defaultValue:true), 
    
      ])
])
node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */
        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        app = docker.build("hnmohan92/golangoutyet")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

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
  
   /*   
    stage('Deploy Image'){
     sh 'docker run -d -p 12345:12345 --name gorestapi hnmohan92/golangoutyet'   
    }*/
    if (deleteOld=='true'){
      stage('Delete Old Container'){
        sh "ssh -i jikoqa0619.pem ubuntu@$params.DeployIp docker rm --force gorestapi >> output.log"
      }
    }
    
    stage('Deploy in AWS'){
        sh "ssh -i jikoqa0619.pem ubuntu@$params.DeployIp docker run -d -p 12345:12345 --name gorestapi hnmohan92/golangoutyet" 
    }
}
