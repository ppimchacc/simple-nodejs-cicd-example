pipeline {
  environment {
    VERCEL_PROJECT_NAME = 'learn-jenkins-app'
    VERCEL_TOKEN = credentials('devops20-vercel-token')
  }

  agent {
    kubernetes {
      yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: my-builder
    image: node:20-alpine
    command:
    - cat
    tty: true
'''
    }
  }

  stages {

    stage('Check Node & NPM') {
      steps {
        container('my-builder') {
          sh 'node --version'
          sh 'npm --version'
        }
      }
    }

    stage('Install Dependencies') {
      steps {
        container('my-builder') {
          sh 'npm ci'
        }
      }
    }

    stage('Test') {
      steps {
        container('my-builder') {
          sh 'npm test'
        }
      }
    }
    stage('Deploy') {
      steps {
        container('my-builder') {
          sh 'npm install -g vercel@latest'
          sh '''
            vercel link --project $VERCEL_PROJECT_NAME --token $VERCEL_TOKEN --yes
            vercel --token $VERCEL_TOKEN --prod --confirm
          '''
        }
      }
    }
  }
}
