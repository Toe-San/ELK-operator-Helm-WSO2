# Helm Chart for deployment of WSO2 API Manager

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

   
