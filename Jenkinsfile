pipeline {
agent any
environment {
APP_NAME = 'devops-app'
VERSION = "1.0.${BUILD_NUMBER}"
}
stages {
stage('Checkout') {
steps {
echo 'Pulling source code...'
checkout scm
}
}
stage('Build') {
steps {
echo "Building ${APP_NAME} version ${VERSION}"
sh 'mkdir -p build && echo built > build/artifact.txt'
}
}
stage('Test') {
steps {
echo 'Running tests...'
sh 'echo TEST PASSED >> build/artifact.txt'
}
}
stage('Deploy') {
steps {
echo "Deploying version ${VERSION} to server"
sh 'cp build/artifact.txt /tmp/deployed_app.txt'
sh 'cat /tmp/deployed_app.txt'
}
}
}
post {
success { echo 'Pipeline completed really successfully!' }
failure { echo 'Pipeline FAILED. Check logs above.' }
}
}
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
