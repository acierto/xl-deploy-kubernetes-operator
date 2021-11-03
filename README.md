# Deploy Kubernetes Operator

Full documentation can be found by this link: 
[https://xebialabs.github.io/xl-deploy-kubernetes-operator](https://xebialabs.github.io/xl-deploy-kubernetes-operator/)

## Steps to generate Deploy scaffolding using the Deploy Helm chart

**Note: Before you proceed you should be connected to your Kubernetes cluster**

1. Create a folder namely 'xld'
    ```
    mkdir xld
    ```
2. Switch to xld folder
    ```
    cd xld
    ```
3. Clone the Deploy Helm chart repository
    ```
    git clone git@github.com:xebialabs/xl-deploy-kubernetes-helm-chart.git
    ```
4. Switch to the cloned repository folder
    ```
    cd xl-deploy-kubernetes-helm-chart
    ```
5. Remove the values file for HAProxy as we will generate the scaffolding with NIGIX support
    ```
    rm -f values-haproxy.yaml
    ```
6. Rename the values file for NGINX to values.yaml
    ```
    mv values-nginx.yaml values.yaml
    ```
7. Update the dependency for the charts
    ```
    helm dependency update .
    ```
8. Remove the lock file generated during updates of charts
    ```
    rm -f Chart.lock
    ```
9. Switch back to the parent folder, you should be in 'xld' folder
    ```
    cd ..
    ```
10. Create the Helm package using the available charts
    ```
    helm package xl-deploy-kubernetes-helm-chart
    ```
11. Remove the folder containing helm charts
    ```
    rm -rf xl-deploy-kubernetes-helm-chart
    ```
12. Rename the tgz file to xld.tgz
    ```
    mv digitalai-deploy-10.1.tgz xld.tgz
    ```
13. Use the operator sdk to initiate Deploy operator project, please make sure you are connected to your Kubernetes cluster otherwise you will get an error
    ```
    operator-sdk init --domain digital.ai --plugins=helm
    ```
14. Create APIs with 'xld' group and using the xld helm chart
    ```
    operator-sdk create api --group=xld --version=v1alpha1 --helm-chart=xld.tgz
    ```
15. We now need to generate the operator controller image. Export one variable to create the image along with tag
    ```
    export OPERATOR_IMG="docker.io/xldevdocker/deploy-operator:1.0.0"
    ```
> If you are regenerating the scaffolding due to changes in helm charts then simply increment/change the image tag
16. Using the Makefile generated by operator sdk, build and push the image to the Docker Hub registry
    ```
    make docker-build docker-push IMG=$OPERATOR_IMG
    ```
