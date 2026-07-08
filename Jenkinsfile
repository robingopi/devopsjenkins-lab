pipeline {
agent any
environment {
VERSION = "2.0.${BUILD_NUMBER}"
DEPLOY_SERVER = credentials('ec2-server-ip')
}
stages {
stage('Checkout') { steps { checkout scm } }
stage('Build') {
steps {
sh 'echo Building version ${VERSION}'
sh 'mkdir -p artifacts'
sh 'zip -r artifacts/app-${VERSION}.zip webapp/'
}
}
stage('Test') {
steps {
sh 'echo Running smoke tests...'
sh '[ -f webapp/index.html ] && echo HTML file OK'
sh 'grep -q DevOps webapp/index.html && echo Content OK'
}
}
stage('Deploy to EC2') {
steps {
sh 'bash deploy.sh ${VERSION}'
}
}
stage('Smoke Test') {
steps {
sh 'curl -f http://localhost/ | grep DevOps'
echo 'Application is live and responding!'
}
}
}
post {
success { echo "Version ${VERSION} deployed successfully" }
failure { echo 'CI/CD Pipeline failed. Deployment aborted.' }
}
}

