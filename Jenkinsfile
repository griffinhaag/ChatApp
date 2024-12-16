node('app-serverNew') {
    def app
 
    stage('Cloning Git') {
        // Cloning the Git repository to the workspace
        checkout scm
    }
 
    stage('SCA-SAST-SNYK-TEST') {
        // Running Snyk security analysis
        snykSecurity(
            snykInstallation: 'Snyk',
            snykTokenId: 'synkid',
            severity: 'critical'
        )
    }
 
    stage('SonarQube Analysis') {
        steps {
            script {
                // Running SonarQube analysis
                def scannerHome = tool 'sonarqube'
                withSonarQubeEnv('sonarqube') {
                    sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=chatapp \
                        -Dsonar.sources=.
                    """
                }
            }
        }
    }
 
    stage('Build-and-Tag') {
        // Building the Docker image
        app = docker.build("griffinh00/nodejschatapp_repo")
    }
 
    stage('Post-to-dockerhub') {
        // Pushing the image to Docker Hub
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
            app.push("latest")
        }
    }
 
    stage('Pull-image-server and deploy') {
        // Deploying the application using Docker Compose
        sh "docker-compose down"
        sh "docker-compose up -d"
    }
}
