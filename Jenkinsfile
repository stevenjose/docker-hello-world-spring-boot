node {
    // Herramienta Maven
    def mvnHome = tool 'maven-3.8.5'
 
    // Configuración de la imagen Docker
    def dockerRepoUrl = "localhost:8083"
    def dockerImageName = "hello-world-java"
    def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:${env.BUILD_NUMBER}"
 
    def dockerImage
 
    stage('Build Docker Image') {
        echo "Construyendo imagen Docker..."

        sh '''
            mkdir -p data
            rm -f data/*.jar
            cp ./target/hello*.jar ./data/app.jar
        '''

        docker.withServer('unix:///var/run/docker.sock') {
            dockerImage = docker.build(dockerImageName)
        }
    }
 
    stage('Build Project') {
        sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore clean package"
    }
 
    stage('Publish Test Results') {
        parallel(
            publishJunitTestsResultsToJenkins: {
                echo "Publicando resultados de pruebas JUnit"
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            },
            publishJunitTestsResultsToSonar: {
                echo "Esta es la rama B (placeholder para Sonar)"
            }
        )
    }
 
    stage('Build Docker Image') {
        echo "Construyendo imagen Docker..."
        // Asegúrate de que la carpeta "data" exista o Dockerfile apunte bien
        sh "mkdir -p data"
        sh "mv ./target/hello*.jar ./data"
 
        docker.withServer('unix:///var/run/docker.sock') {
dockerImage = docker.build(dockerImageName)
        }
    }
 
    stage('Deploy Docker Image') {
        echo "Docker Image Tag: ${dockerImageTag}"
        sh "docker login -u admin -p 123456 ${dockerRepoUrl}"
        sh "docker tag ${dockerImageName} ${dockerImageTag}"
        sh "docker push ${dockerImageTag}"
        sh "docker logout ${dockerRepoUrl}"
    }
}
