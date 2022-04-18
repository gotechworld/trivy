pipeline {
    
    agent { label 'linux'}
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    environment {
        TEMPLATE_PATH="\"@/home/jenkins/docker/trivy/templates/html.tpl\""
    }
    
    stages {
        stage ('Pull Docker image') {
            steps {
                echo "Pull image from registry"
                sh "whoami"
                sh "sudo docker pull ${DOCKER_IMAGE}:${VERSION}"
            }
        }
        
        stage ('Analyze with Trivy') {
            steps {
                echo "Analyze it with Trivy"
                sh "trivy image --ignore-unfixed --format template --template ${TEMPLATE_PATH} \
                --output cve_report.html ${DOCKER_IMAGE}:${VERSION}"
            }
        }
        
    }
    post {
        always {
            archiveArtifacts artifacts: 'cve_report.html', followSymlinks: false, fingerprint: true
            
            publishHTML ([
                allowMissing: false,
                alwaysLinkToLastBuild: false,
                keepAll: true,
                reportDir: '.',
                reportFiles: 'cve_report.html',
                reportName: "CVE-Report"
            ])
        }
        
    }
}
