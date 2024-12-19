## WSO2 API Manager deploy with Helm Chart | Deploy ECK in Kubernetes cluster | Cert-Manager with Let's Encrypt Deploy on K8S

## 1. Helm Chart for deployment of WSO2 API Manager


Resources for building a Helm chart for deployment of [Single Node API Manager](https://apim.docs.wso2.com/en/4.0.0/install-and-setup/setup/single-node/all-in-one-deployment-overview/#single-node-deployment).

![WSO2 API Manager Single Node deployment](https://apim.docs.wso2.com/en/4.0.0/assets/img/setup-and-install/single-node-apim-deployment.png)

For advanced details on the deployment pattern, please refer to the official
[documentation](https://apim.docs.wso2.com/en/4.0.0/install-and-setup/setup/single-node/all-in-one-deployment-overview/#active-active-deployment).


## Prerequisites

* WSO2 product Docker images used for the Kubernetes deployment.

* Install [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), [Helm](https://helm.sh/docs/intro/install/)
  and [Kubernetes client](https://kubernetes.io/docs/tasks/tools/install-kubectl/) in order to run the steps provided in the
  following quick start guide.<br><br>

* An already setup [Kubernetes cluster](https://kubernetes.io/docs/setup).<br><br>

* Install [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/deploy/).<br><br>

##### Clone the Helm Resources for WSO2 API Manager Git repository.

        git clone https://github.com/Toe-San/ELK-operator-Helm-WSO2.git

1. Checkout the Helm Resources for WSO2 API Manager Git repository using git clone

        helm install <RELEASE_NAME> helm-apim-mai-ts/all-in-one -f helm-apim-mai-ts/all-in-one/ -n <NAMESPACE>

2. Access Management Console. <br>
   a. Obtain the external IP (EXTERNAL-IP) of the Ingress resources by listing down the Kubernetes Ingresses.

        kubectl get ing -n <NAMESPACE>

        NAME                                               HOSTS                                ADDRESS          PORTS      AGE
        wso2am-single-node-am-gateway-ingress              <RELEASE_NAME>-gateway               <EXTERNAL-IP>    80, 443    7m
        wso2am-single-node-am-ingress                      <RELEASE_NAME>-am                    <EXTERNAL-IP>    80, 443    7m
        wso2am-single-node-am-websub-ingress               <RELEASE_NAME>-websub                <EXTERNAL-IP>    80, 443    7m


   b. Add the above hosts as entries in /etc/hosts file as follow  (For local deploy)
   
        <EXTERNAL-IP> <RELEASE_NAME>-am
        <EXTERNAL-IP> <RELEASE_NAME>-gateway
        <EXTERNAL-IP> <RELEASE_NAME>-websub

<br>
<br>
<br>

## 2. Elastic Cloud on Kubernetes (ECK)

![image](https://github.com/user-attachments/assets/5a208b74-a01a-4958-9189-6c2ef154c87a)


Elastic Cloud on Kubernetes automates the deployment, provisioning, management, and orchestration of Elasticsearch, Kibana, APM Server, Enterprise Search, Beats, Elastic Agent, Elastic Maps Server, and Logstash on Kubernetes based on the operator pattern.

Current features:

*  Elasticsearch, Kibana, APM Server, Enterprise Search, and Beats deployments
*  TLS Certificates management
*  Safe Elasticsearch cluster configuration & topology changes
*  Persistent volumes usage
*  Custom node configuration and attributes
*  Secure settings keystore updates

Supported versions:

*  Kubernetes 1.27-1.31
*  OpenShift 4.12-4.17
*  Elasticsearch, Kibana, APM Server: 6.8+, 7.1+, 8+
*  Enterprise Search: 7.7+, 8+
*  Beats: 7.0+, 8+
*  Elastic Agent: 7.10+ (standalone), 7.14+, 8+ (Fleet)
*  Elastic Maps Server: 7.11+, 8+
*  Logstash 8.7+

Check the [Quickstart](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-quickstart.html) to deploy your first cluster with ECK.

If you want to contribute to the project, check our [contributing guide](CONTRIBUTING.md) and see [how to setup a local development environment](dev-setup.md).

For general questions, please see the Elastic [forums](https://discuss.elastic.co/c/eck).

1. Install custom resource definitions:

        kubectl create -f https://download.elastic.co/downloads/eck/2.15.0/crds.yaml

    The following Elastic resources have been created:

        customresourcedefinition.apiextensions.k8s.io/agents.agent.k8s.elastic.co created
        customresourcedefinition.apiextensions.k8s.io/apmservers.apm.k8s.elastic.co created
        customresourcedefinition.apiextensions.k8s.io/beats.beat.k8s.elastic.co created
        customresourcedefinition.apiextensions.k8s.io/elasticmapsservers.maps.k8s.elastic.co created
        customresourcedefinition.apiextensions.k8s.io/elasticsearches.elasticsearch.k8s.elastic.co created
        customresourcedefinition.apiextensions.k8s.io/enterprisesearches.enterprisesearch.k8s.elastic.co created
        customresourcedefinition.apiextensions.k8s.io/kibanas.kibana.k8s.elastic.co created
        customresourcedefinition.apiextensions.k8s.io/logstashes.logstash.k8s.elastic.co created

2. Install the operator with its RBAC rules:

       kubectl apply -f https://download.elastic.co/downloads/eck/2.15.0/operator.yaml

3. Monitor the operator logs:

       kubectl -n elastic-system logs -f statefulset.apps/elastic-operator

4. Get the credentials.

   A default user named elastic is created by default with the password stored in a Kubernetes secret:
   ## LINUX

       kubectl get secret elasticsearch-es-elastic-user -o go-template='{{.data.elastic | base64decode}}' -n elk

   ## PowerShell
   Retrieve the base64-encoded password from the Kubernetes secret  
    
       $Base64Password = kubectl get secret elasticsearch-es-elastic-user -n elk -o jsonpath="{.data.elastic}"  

   Decode the base64-encoded password  
    
       $PASSWORD = [System.Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($Base64Password))  

   Output the decoded password  

       Write-Output $PASSWORD

<br>
<br>
<br>

## 3. Cert-Manager

![image](https://github.com/user-attachments/assets/84a396fd-5822-4578-ac07-85be58096281)

![image](https://github.com/user-attachments/assets/eb8b4de1-3a09-4791-b22b-e1c480e25aff)


Now, lets start with installation of Cert-Manager.

Installing Cert-Manager: Let’s dive into the step-by-step installation process for Cert-Manager:

* Prerequisites: Before proceeding with the installation, ensure the following prerequisites are met:

  1. A running Kubernetes cluster (1.16 or later).
  2. kubectl command-line tool installed and configured to access your Kubernetes cluster.

Step 1: Install the Cert-Manager CRDs: The first step is to install the Custom Resource Definitions (CRDs) required by Cert-Manager. These CRDs define the necessary resources for managing certificates.

        kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.16.2/cert-manager.yaml

Create a Cloudflare API Token Secret
Create a Kubernetes secret to store your Cloudflare API token. Replace <your-cloudflare-api-token> with your actual token.

    kubectl create secret generic cloudflare-api-token-secret \
      --from-literal=api-token=<your-cloudflare-api-token> \
      -n cert-manager

<br> 
Step 2:
Create a ClusterIssuer for Let's Encrypt
Create a ClusterIssuer resource to configure cert-manager to use Let's Encrypt with the DNS-01 challenge via Cloudflare.

Save the following YAML to a file, e.g., cluster-issuer.yaml:

    apiVersion: cert-manager.io/v1
    kind: ClusterIssuer
    metadata:
      name: letsencrypt-wildcard-issuer
    spec:
      acme:
        server: https://acme-v02.api.letsencrypt.org/directory
        email: achawlay-toe@san.com
        privateKeySecretRef:
          name: letsencrypt-wildcard-key
        solvers:
        - dns01:
            cloudflare:
              email: achawlay-toe@san.com
              apiTokenSecretRef:
                name: cloudflare-api-token-secret
                key: api-token

<br>  

    kubectl create -f cluster-issuer.yml

<br> 
Step 3:
Request a Wildcard Certificate
Create a Certificate resource to request a wildcard certificate for your domain.

Save the following YAML to a file, e.g., certificate.yaml:

    apiVersion: cert-manager.io/v1
    kind: Certificate
    metadata:
      name: wildcard-toesan-com
      namespace: <your namespace>
    spec:
      secretName: wildcard-ts-tls 
      issuerRef:
        name: letsencrypt-wildcard-issuer
        kind: ClusterIssuer
      commonName: "*.toesan.com"
      dnsNames:
      - "*.toesan.com"
      - "toesan.com"
        
<br>

    kubectl create -f certificate.yml

<br>

    kubectl get clusterissuer -A
<br>

    kubectl get certificate -A

        
        
