# worksample-objective-1-2

In this repository, there are two yaml files to create Nginx and whoami pods with respective services and ingress declaratively on k8s cluster. There is also a GitHub Action workflow to automate the deployment on a GKE cluser. 

## File Descriptions 
| **FIle** | **Purpose** |
| --- | --- |
| `nginx_svc.yaml` | creates an nginx pod and a service to expose ports declaratively |
| `whoami_svc_ingress.yaml` | creates an whoami pod with its service to expose ports, and a ingress declaratively|
| `objective-12.yml` | Workflow file authenticates to GKE cluster on GCP, downloads repository, creates resources and checks if deployed  |

There are two deployment 
1. Automated deployment with Github Actions on to a GKE cluster
2. Manuel deployment to any k8s cluster

## 1. Automated deployment with Github Actions on to a GKE cluster
## Prerequisites
 1. GCP Service account credentials should be added to GitHub actions as secrets

    - Create an GCP IAM service account, which should have basic owner previledges
    - Add a access key to the GCP IAM service account whis downloads the key as a json file 
    - From the repository’s GitHub Actions settings, add a secret with its key using the json file as it is below. 

        | **Secret Name** | **Secret** |
        | :---: | :---: |
        | `SERVICE_ACCOUNT` | copy the contend of the json file (service account key)  |

2.	Goto .github\workflows\objective-12.yml file and update environment variables for the cluster and repository details
    ```
    env:
    region:             "us-east5-b"
    repository:         "https://github.com/Meydan52/worksample-objective-1-2.git"
    cluster_name:       "cluster-1"

    ```
    | **Variable** | **Purpose** |
    | --- | --- |
    | `region  ` | Cluster region |
    | `repository` | This repository to be cloned to the environment |
    | `cluster_name` | GKE cluster name | 

## Deploy into GKE cluster 
1. Commit changes to the main branch which trigers the pipeline to iniciate the deployment
2. From the cloud shell console or bastion host, check if the ingress created and got a public IP properly. Thid process may takes up to 6 mins.
    ```
    kubectl get ingress whoami-ingress
    ```
3. Test resources with HEAD and GET requests
    - Get the public IP from the ingress
        ```
        kubectl describe ingress whoami-ingress | grep Address: | awk '{print$2}'
        ```
    - Test HEAD request
        ```
        curl -I http://<public ip>
        ```
    - GET request to nginx
        ```
        curl http://<public ip>/
        ```
    - GET request to whoami
        ```
        curl http://<public ip>/whoami
        ```

## 2. Manuel deployment to any k8s cluster
## Prerequisites

Cone the repository desired k8s environment and get in to the directory

    ```
    git clone git@github.com:Meydan52/worksample-objective-1-2.git
    cd worksample-objective-1-2
    ```
## Deploy into GKE cluster 
1. create resources using two yaml files in the directory
    ```
    kubectl create -f nginx_svc.yaml
    kubectl create -f whoami_svc_ingress.yaml
    ```

2. Check resources if created
    ```
    kubectl get pods -o wide
    kubectl get svc -o wide
    kubectl describe ingress whoami-ingress

    ```
3. Test resources with HEAD and GET requests
    - Get the public IP from the ingress
        ```
        kubectl describe ingress whoami-ingress | grep Address: | awk '{print$2}'
        ```
    - Test HEAD request
        ```
        curl -I http://<public ip>
        ```
    - GET request to nginx
        ```
        curl http://<public ip>/
        ```
    - GET request to whoami
        ```
        curl http://<public ip>/whoami
        ```
