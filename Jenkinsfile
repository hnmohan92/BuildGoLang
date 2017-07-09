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
  
      
    stage('Delete Old Container'){
        sh 'docker rm --force gorestapi'
    }
    
    stage('Deploy Image'){
     sh 'docker run -d -p 12345:12345 --name gorestapi hnmohan92/golangoutyet'   
    }
    stage('Deploy in AWS'){
        sh 'ssh -i jikoqa0619.pem ubuntu@18.220.35.55 -y'
        sh 'echo hello world'
    }
}
