pipeline {
   agent {
      label 'UAT'
   }
   environment {
      PROJECT_ID = 'my-practice-project-377116'
      CLUSTER_NAME = 'my-practice-project-377116-gke'
      LOCATION = 'us-west1-a'
      CREDENTIALS_ID = credentials('gcp-auth-jenkins')
      DOCKER_REGISTRY = 'https://registry.hub.docker.com'
   }

   stages {
      stage('Scm Checkout') {
            steps {
            checkout scm
            }
      }
      stage('creds test') {
            steps {
               withCredentials([file(credentialsId: 'gcp-auth-jenkins', variable: 'CREDENTIALS_ID')]) {
               // some block
               }
            }
      }

      stage('Build') {
         steps {
            echo 'Cleaning and packaging...'
            sh 'mvn clean package'
         }
      }

      stage('Test') {
            steps {
            echo 'Testing...'
            sh 'mvn test'
            }
      }
      stage('Build Docker Image') {
            steps {
            sh 'whoami'
            sh "docker build -t praveenbabu135/k8sdemo:${env.BUILD_ID} ."
            }
      }
      stage('Push Docker Image') {
         steps {
            // withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
            // // some block
            // }
            script {
               docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        // myimage.push("${env.BUILD_ID}")

               }
            }
               //   sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD $DOCKER_REGISTRY'
                 sh 'docker push praveenbabu135/k8sdemo:${env.BUILD_ID}'
            }
         }

      stage('Deploy to K8s') {
         steps {
            echo 'Deployment started ...'
            sh 'ls -ltr'
            sh 'pwd'
            sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
            step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            echo 'Deployment Finished ...'
         }
      }
      }
   }
