podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.7.3', command: 'cat', ttyEnabled: true)
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]) {

node('mypod') {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        container('docker') {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', 
                credentialsId: 'docker-hub-credentials',
                usernameVariable: 'DOCKER_HUB_USER', 
                passwordVariable: 'DOCKER_HUB_PASSWORD']]) {

            sh """
                docker build --build-arg=NO_PROXY="*.fau.edu,localhost,127.0.0.1,10.19.64.186,10.19.64.187" --build-arg=HTTP_PROXY=http://octopus.fau.edu:3128 --build-arg=HTTPS_PROXY=http://octopus.fau.edu:3128 -t ${env.DOCKER_HUB_USER}/hellonode:${env.BUILD_NUMBER} .
                """
            sh "docker login -u ${env.DOCKER_HUB_USER} -p ${env.DOCKER_HUB_PASSWORD} "
            sh "docker push ${env.DOCKER_HUB_USER}/hellonode:${env.BUILD_NUMBER} "
            }
            //app = docker.build("getintodevops/hellonode")
        }
    }

    //stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

   //     app.inside {
   //         sh 'echo "Tests passed"'
   //     }
   // }

    //stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        //docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
        //    app.push("${env.BUILD_NUMBER}")
        //    app.push("latest")
        //}
    //}
}
}
