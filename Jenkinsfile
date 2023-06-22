pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                // Your build steps here
                // For example, building a Docker image:
                sh 'docker build -t myimage:latest .'
            }
        }
        
        stage('Scan') {
            steps {
                script {
                    def clairImage = 'quay.io/coreos/clair:v2.1.7'
                    def clairReport = 'clair-report.json'
                    
                    // Pull and run Clair container
                    sh "docker run -d --name clair -p 6060:6060 $clairImage"
                    
                    // Run Clair scanner on the built image
                    sh "docker run -v /var/run/docker.sock:/var/run/docker.sock --network host -e CLAIR_ADDR=http://localhost:6060 -e OUTPUT=$clairReport --rm arminc/clair-scanner myimage:latest"
                    
                    // Stop and remove Clair container
                    sh "docker stop clair"
                    sh "docker rm clair"
                    
                    // Archive the Clair report as an artifact
                    archiveArtifacts artifacts: "$clairReport"
                }
            }
        }
    }
    
    post {
        always {
            // Cleanup Docker images
            sh 'docker image prune -f'
        }
    }
}
