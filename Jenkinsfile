node {
    // Herramienta Maven (asegúrate que esté configurada en Jenkins → Global Tool Configuration)
    def mvnHome = tool 'maven-3.8.5'
 
    // Nombre del repositorio Docker privado (Nexus local)
    def dockerRepoUrl = "localhost:8083"
    def dockerImageName = "hello-world-java"
    def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:${env.BUILD_NUMBER}"
    def dockerImage
 
    stage('Clone Repo') {
git 'https://github.com/dstar55/docker-hello-world-spring-boot.git'
    }
 
    stage('Build Project') {
        // Compila el proyecto usando Maven
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
        sh "mv ./target/hello*.jar ./data"  // Mueve el jar al contexto del Dockerfile
	script {
		dockerImage = docker.build("${dockerImageName}")
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
