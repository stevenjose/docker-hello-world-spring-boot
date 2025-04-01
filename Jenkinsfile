node {
    def dockerRepoUrl = "localhost:8083"
    def dockerImageName = "hello-world-java"
    def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:${env.BUILD_NUMBER}"
    def dockerImage
 
    stage('Checkout Repo') {
        // Jenkins ya lo hace si usas SCM, pero puedes dejarlo como referencia
        checkout scm
    }
 
    stage('Build Docker Image') {
        echo "Construyendo imagen multietapa desde Dockerfile..."
        docker.withServer('unix:///var/run/docker.sock') {
dockerImage = docker.build(dockerImageName)
        }
    }
 
    stage('Push to Nexus') {
        echo "Docker Image Tag: ${dockerImageTag}"
        sh "docker login -u admin -p 123456 ${dockerRepoUrl}"
        sh "docker tag ${dockerImageName} ${dockerImageTag}"
        sh "docker push ${dockerImageTag}"
        sh "docker logout ${dockerRepoUrl}"
    }
}
