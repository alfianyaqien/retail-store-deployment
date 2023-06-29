pipeline {
  agent any
  
  parameters {
    // Define parameters if needed
  }

  environment {
    registry = "alfianyaqien/devopstest"
    registryCredential = 'dockerhub'
  }
  
  stages {
    stage('Build and Test') {
      steps {
        // Build and test Spring Boot UI
        dir('ui') {
          sh 'mvn clean install -DskipTests'
          sh 'mvn test'
          sh 'mvn verify -DskipUnitTests'
          sh 'mvn checkstyle:checkstyle'
        }
        
        // Build and test Golang Product Catalog API
        dir('catalog') {
          sh 'go build -o main main.go'
        }
        
        // Build and test Spring Boot User Shopping Carts API
        dir('cart') {
          sh 'mvn clean install -DskipTests'
          sh 'mvn test'
          sh 'mvn verify -DskipUnitTests'
          sh 'mvn checkstyle:checkstyle'
        }
        
        // Build and test Spring Boot User Orders API
        dir('orders') {
          sh 'mvn clean install -DskipTests'
          sh 'mvn test'
          sh 'mvn verify -DskipUnitTests'
          sh 'mvn checkstyle:checkstyle'
        }
        
        // Build and test NestJS Checkout API
        dir('checkout') {
          sh 'yarn install'
        }
      }
    }

    stage('Build and Push App Image') {
      steps {
        dir('ui') {
          script {
            dockerImage = docker.build registry + ":V$BUILD_NUMBER-ui"
            docker.withRegistry('', registryCredential) {
              dockerImage.push("V$BUILD_NUMBER-ui")
            }
          }
        }
        sh "docker rmi -f $registry:V$BUILD_NUMBER-ui"

        dir('catalog') {
          script {
            dockerImage = docker.build registry + ":V$BUILD_NUMBER-catalog"
            docker.withRegistry('', registryCredential) {
              dockerImage.push("V$BUILD_NUMBER-catalog")
            }
          }
        }
        sh "docker rmi -f $registry:V$BUILD_NUMBER-catalog"

        dir('cart') {
          script {
            dockerImage = docker.build registry + ":V$BUILD_NUMBER-cart"
            docker.withRegistry('', registryCredential) {
              dockerImage.push("V$BUILD_NUMBER-cart")
            }
          }
        }
        sh "docker rmi -f $registry:V$BUILD_NUMBER-cart"

        dir('orders') {
          script {
            dockerImage = docker.build registry + ":V$BUILD_NUMBER-orders"
            docker.withRegistry('', registryCredential) {
              dockerImage.push("V$BUILD_NUMBER-orders")
            }
          }
        }
        sh "docker rmi -f $registry:V$BUILD_NUMBER-orders"

        dir('checkout') {
          script {
            dockerImage = docker.build registry + ":V$BUILD_NUMBER-checkout"
            docker.withRegistry('', registryCredential) {
              dockerImage.push("V$BUILD_NUMBER-checkout")
            }
          }
        }
        sh "docker rmi -f $registry:V$BUILD_NUMBER-checkout"

        dir('assets') {
          script {
            dockerImage = docker.build registry + ":V$BUILD_NUMBER-assets"
            docker.withRegistry('', registryCredential) {
              dockerImage.push("V$BUILD_NUMBER-assets")
            }
          }
        }
        sh "docker rmi -f $registry:V$BUILD_NUMBER-assets"
      }
    }

    stage('Deploy App') {
      agent {label 'KOPS'}
      // Deploy Spring Boot UI
      dir('ui') {
        // Deploy using Kubernetes manifests or Helm charts
        sh "helm upgrade --install --force retail-ui charts/ui --set appImage=${registry}:V${BUILD_NUMBER}-ui --namespace prod"
      }

      // Deploy Golang Product Catalog API
      dir('catalog') {
        // Deploy using Kubernetes manifests or Helm charts
        sh "helm upgrade --install --force retail-catalog charts/catalog --set appImage=${registry}:V${BUILD_NUMBER}-catalog --namespace prod"
      }

      // Deploy Spring Boot User Shopping Carts API
      dir('cart') {
        // Deploy using Kubernetes manifests or Helm charts
        sh "helm upgrade --install --force retail-cart charts/carts --set appImage=${registry}:V${BUILD_NUMBER}-cart --namespace prod"
      }

      // Deploy Spring Boot User Orders API
      dir('orders') {
        // Deploy using Kubernetes manifests or Helm charts
        sh "helm upgrade --install --force retail-orders charts/orders --set appImage=${registry}:V${BUILD_NUMBER}-orders --namespace prod"
      }

      // Deploy NestJS Checkout API
      dir('checkout') {
        // Deploy using Kubernetes manifests or Helm charts
        sh "helm upgrade --install --force retail-checkout charts/checkout --set appImage=${registry}:V${BUILD_NUMBER}-checkout --namespace prod"
      }

      // Deploy NGINX for serving static assets
      dir('assets') {
        // Deploy using Kubernetes manifests or Helm charts
        sh "helm upgrade --install --force retail-assets charts/assets --set appImage=${registry}:V${BUILD_NUMBER}-assets --namespace prod"
      }
    }
  }
}
