## Jenkins  Multibranch Pipeline

### Pre-reqs
1. Jenkins Master
1. Elastic Kubernetes clusters
1. AWS profile to access target account i.e, `dev` and `qa`


### Setup Jenkins Pipeline
1. Add Credential to access DockerHub

    Jenkins -> Credentials  -> System -> Add Crdentials
    ```
        Kind: Username with password
        Scope: Global
        Username: <Username to access Docker registry>
        Password: <password>
        ID : dockerHub

1. Add Credential to access GitHub
    
    Jenkins -> Credentials  -> System -> Add Credentials
    ```
        Kind: Username with password
        Scope: Global
        Username: <Username to access Docker registry>
        Password: <password>
        ID : GitHub

1. Setup Mult-branch pipeline
    
    - Jenkins -> New Item  -> Enter Item Name -> Select Multibranch Pipeline -> Click OK
    
    - Display Name : custom-nginx
    - Description:  custom-nginx
    - Add source -> GitHub ->  Credentials (GitHub) -> Repository URL (link to GitHub project )
    
1. Webhook

### Run Pipeline

1. Clone this project
1. Update `Jenkinsfile` and `deployment.yaml` with your registry. i.e,  replace `samsonbabu/nginx-jenkins-demo` with your own
1. Create `develop` branch and edit index.html

   Change `Welcome to nginx`  -> `Welcome to nginx build by [your name]`

        git checkout -b develop
        git add .
        git commit -m "Change welcome message"
        git push --set-upstream origin develop



    *  Jenkins Multibranch Pipeline will build image with custom `index.html` push to defined registry and deploy to kubernetes in aws dev account.

        
        kubectl port-forward service/my-nginx 8080:80
    Click http://localhost:8080


1. Create  PR

    * Check Jenkins UI,  Multibranch pipeline will execute defined `tests.sh`

1. Merge change to `master` branch
    * At this point Multibranch Pipeline wil build image again with custom `index.html` push to regitry and deploy to EKS cluster in `qa`
