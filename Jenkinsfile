pipeline {
  agent {
        docker {
            image "forsingh/helm-installer:latest"
            label "master"
            args "-u root:root --entrypoint=''"
        }
    }
  stages {
    stage('Grab Creds & deploy to AKS') {
      steps {
        withCredentials([azureServicePrincipal('aks-thru-sp')]) {
        sh '''az login --service-principal --username $AZURE_CLIENT_ID --password $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID
az aks get-credentials --name sym-stratus-dev-rg-nsingh2-aks-norbac --resource-group sym-stratus-dev-rg-nsingh2'''
        sh 'helm ls --all --short | xargs -L1 helm delete'
        sh '''helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm install stable/grafana --version 4.4.0 --generate-name'''
          sh 'cd ~'
          sh '''git clone git@github.com:synergymachines/charts.git
cd charts/charts
helm install backend --generate-name . 
helm install nginx-ingress --generate-name ''' 
        sh '''rm ~/.kube/config
cd $WORKSPACE
rm -rf kubernetes-ingress'''
        }
      }
    }

  }
  post {
        always{
            cleanWs()
        }
    }
}
