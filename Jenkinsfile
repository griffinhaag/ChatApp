node('app-serverNew')
{
    def app
    stage('Cloning Git')
    {
    /* Let's make sure we have the repository cloned to our workspace */
    checkout scm
    }

      stage('SCA-SAST-SNYK-TEST') 
      {
       agent 
       
       {
         label 'app-serverNew'
       }
       
         snykSecurity(
            snykInstallation: 'Snyk',
            snykTokenId: 'synkid',
            severity: 'critical'
         )
       }
    stage('SonarQube Analysis') {

            agent {

                label 'app-serverNew'

            }

            steps {

                script {

                    def scannerHome = tool 'sonarqube'

                    withSonarQubeEnv('sonarqube') {

                        sh "${scannerHome}/bin/sonar-scanner \

                            -Dsonar.projectKey=chatapp \

                            -Dsonar.sources=."

                    }

                }

            }

        }
 
    stage('Build-and-Tag')
    {
        /* This builds the actual image; 
        * This is synonymous to docker build on the command line */
        app =docker.build("griffinh00/nodejschatapp_repo")
    }
    stage('Post-to-dockerhub')
    {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials')
        {
         app.push("latest")
        }
    }

    stage('Pull-image-server and deploy')
    {
        sh "docker-compose down"
        sh "docker-compose up -d"
    }

}
