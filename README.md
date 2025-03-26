# CapStone Project: Implementing a Multi-Environment Application Deployment with Kustomize

## <ins>Hypothetical Use Case</ins>: You are tasked with deploying a web application in a Kubernetes environment. The application will have different configurations for development, staging and production environments. Your goal is to utilize Kustomize to manage these configurations efficiently and integrate the process into a CI/CD pipeline.

## <ins>Tasks</ins>: 

### <ins>Task 1: Set up your project</ins>

  - Create a new project directory named **kustomize-capstone**

      - I used the following command line to create the directory

            mkdir kustomize-capstone

![Screenshot (354)](https://github.com/user-attachments/assets/798e0125-7bca-4739-bb72-3b32991792bd)

  - Inside the directory, structure it with Kustomize conventions: **base/** and **overlays/** (subdirectories for dev, staging and prod).

      - I used the following command line to create all the directories and subdirectories

            mkdir base mkdir overlays mkdir overlays/dev mkdir overlays/staging mkdir overlays/prod

![Screenshot (355)](https://github.com/user-attachments/assets/489c390b-8b04-47e4-8384-0574b81e02f7)

### <ins>Task 2: Initialize Git</ins>

  - Initialize a Git repository in your **kustomze-capstone** directory.

      - I navigated to the "source control" panel on vscode and selected "initialize repository"
   
![Screenshot (356)](https://github.com/user-attachments/assets/69fa760e-e175-4d74-be38-b8728ff61751)

  - Create a **.gitignore** file to exclude unnecessary files.

      - I used the "touch .gitignore" command line to create the .gitignore file and then input the following code into the file so as to exclude unnecessary files
                     
                  ### VisualStudioCode ###
                  .vscode/*
                  !.vscode/settings.json
                  !.vscode/tasks.json
                  !.vscode/launch.json
                  !.vscode/extensions.json
                  !.vscode/*.code-snippets
                  
                  # Local History for Visual Studio Code
                  .history/
                  
                  # Built Visual Studio Code Extensions
                  *.vsix
                  
                  ### VisualStudioCode Patch ###
                  # Ignore all local history of files
                  .history
                  .ionide
                  
                  ### Windows ###
                  # Windows thumbnail cache files
                  Thumbs.db
                  Thumbs.db:encryptable
                  ehthumbs.db
                  ehthumbs_vista.db
                  
                  # Dump file
                  *.stackdump
                  
                  # Folder config file
                  [Dd]esktop.ini
                  
                  # Recycle Bin used on file shares
                  $RECYCLE.BIN/
                  
                  # Windows Installer files
                  *.cab
                  *.msi
                  *.msix
                  *.msm
                  *.msp
                  
                  # Windows shortcuts
                  *.lnk
                  
                  # End

![Screenshot (357)](https://github.com/user-attachments/assets/5de60487-3a07-4412-a892-1173e16541c1)

### <ins>Task 3: Define Base Configuration</ins>

  - In the **base/** directory, define Kubernetes resources like Deployment, Service, etc., for your web application.

      - below is the deployment.yaml file

                apiVersion: apps/v1
                kind: Deployment
                metadata:
                  name: my-app
                  labels:
                    app: my-app
                spec:
                  replicas: 2
                  selector:
                    matchLabels:
                      app: my-app
                  template:
                    metadata:
                      labels:
                        app: my-app
                    spec:
                      containers:
                        - name: my-app-container
                          image: my-app:latest # Replace with actual image and tag
                          ports:
                            - containerPort: 8080
                          env:
                            - name: ENVIRONMENT
                              valueFrom:
                                configMapKeyRef:
                                  name: my-app-config
                                  key: environment
                            - name: DATABASE_URL
                              valueFrom:
                                secretKeyRef:
                                  name: my-app-secrets
                                  key: database_url
                          resources:
                            limits:
                              memory: "512Mi"
                              cpu: "500m"
                            requests:
                              memory: "256Mi"
                              cpu: "250m"
                ---
                apiVersion: v1
                kind: ConfigMap
                metadata:
                  name: my-app-config
                  labels:
                    app: my-app
                  
                data:
                  environment: "development"  # Change per environment (e.g., production, staging)
                ---
                apiVersion: v1
                kind: Secret
                metadata:
                  name: my-app-secrets
                  labels:
                    app: my-app
                  
                type: Opaque
                stringData:
                  database_url: "postgres://user:password@host:port/dbname"

![Screenshot (358)](https://github.com/user-attachments/assets/16917636-074e-4acd-bf8d-21bc0466c981)

  - Create a **kustomize.yaml** file in **base/** to include these resources.

      - Below is the kustomize.yaml file in the "base/" directory
   
              apiVersion: kustomize.config.k8s.io/v1beta1
              kind: Kustomization
              
              resources:
                - deployment.yaml

![Screenshot (359)](https://github.com/user-attachments/assets/30f1d6c0-cec3-48fb-af72-2e5359811de4)

### <ins>Task 4: Create Environment-Specific Overlays:</ins>

  - In each subdirectory of **overlays/**, create a **kustomize.yaml** that customizes the base configuration for that environment.

      - Development

            apiVersion: kustomize.config.k8s.io/v1beta1
            kind: Kustomization
            
            resources:
              - ../base
            
            patches:
              - target:
                  kind: ConfigMap
                  name: my-app-config
                patch: |-
                  - op: replace
                    path: /data/environment
                    value: "development"

![Screenshot (362)](https://github.com/user-attachments/assets/b81d4fed-853b-4703-be10-9d10a90c3881)

  - Staging

            apiVersion: kustomize.config.k8s.io/v1beta1
            kind: Kustomization
            
            resources:
              - ../base
            
            patches:
              - target:
                  kind: ConfigMap
                  name: my-app-config
                patch: |-
                  - op: replace
                    path: /data/environment
                    value: "staging"

![Screenshot (364)](https://github.com/user-attachments/assets/00242d76-b73a-48fd-bd04-8c359faa0d73)
      
   - Production

            apiVersion: kustomize.config.k8s.io/v1beta1
            kind: Kustomization
            
            resources:
              - ../base
            
            patches:
              - target:
                  kind: ConfigMap
                  name: my-app-config
                patch: |-
                  - op: replace
                    path: /data/environment
                    value: "production"
        
![Screenshot (363)](https://github.com/user-attachments/assets/6bfc9c77-28a4-419d-989f-bae96077d123)

  - Implement variations for each environment (e.g., different replica counts, resource limits, or environment variables).

      - I added a "patch.yaml" file to each environment which implements the different replica counts and resource limits
   
          - Development
       
                apiVersion: apps/v1
                kind: Deployment
                metadata:
                  name: my-app
                spec:
                  replicas: 1  # Development environment uses a single replica
                  template:
                    spec:
                      containers:
                        - name: my-app-container
                          resources:
                            limits:
                              memory: "256Mi"
                              cpu: "250m"
                            requests:
                              memory: "128Mi"
                              cpu: "100m"
          

   
          - Staging
       
                  apiVersion: apps/v1
                  kind: Deployment
                  metadata:
                    name: my-app
                  spec:
                    replicas: 3  # Staging environment uses moderate replica count for testing
                    template:
                      spec:
                        containers:
                          - name: my-app-container
                            resources:
                              limits:
                                memory: "1Gi"
                                cpu: "750m"
                              requests:
                                memory: "512Mi"
                                cpu: "250m"

  
       
          - Production

                apiVersion: apps/v1
                kind: Deployment
                metadata:
                  name: my-app
                spec:
                  replicas: 5  # Production environment uses multiple replicas for scalability
                  template:
                    spec:
                      containers:
                        - name: my-app-container
                          resources:
                            limits:
                              memory: "2Gi"
                              cpu: "1"
                            requests:
                              memory: "1Gi"
                              cpu: "500m"



### <ins>Task 5: Integrate with a CI/CD Pipleline</ins>

  - Choose a CI/CD platform (e.g., GitHub Actions, Jenkins)

      - I decided to choose GitHub Actions
        

  - Set up a pipeline that deploys your application using Kustomize. The pipeline should trigger on code changes.

<ins>Pipeline</ins>

                      name: CI/CD Pipeline
                      
                      on:
                        push:
                          branches:
                            - main
                        pull_request:
                          branches:
                            - main
                      
                      jobs:
                        deploy:
                          runs-on: ubuntu-latest
                          steps:
                            - name: Checkout repository
                              uses: actions/checkout@v3
                      
                            - name: Set up Kustomize
                              run: |
                                curl -sLo kustomize.tar.gz "https://github.com/kubernetes-sigs/kustomize/releases/latest/download/kustomize_linux_amd64.tar.gz"
                                tar -xzf kustomize.tar.gz
                                chmod +x kustomize
                                sudo mv kustomize /usr/local/bin/
                      
                            - name: Authenticate with Kubernetes
                              uses: azure/k8s-set-context@v3
                              with:
                                method: service-account
                                k8s-url: ${{ secrets.K8S_CLUSTER_URL }}
                                k8s-secret: ${{ secrets.K8S_SECRET }}
                      
                            - name: Deploy using Kustomize
                              run: |
                                kubectl apply -k ./kustomize/overlays/prod



### <ins>Task 6: Test the CI/CD Pipeline</ins>

  - Make changes in your Kustomize configurations and push to your repository

      - I made modifications to the replica counts in the Kustomize configurations in the repository
      - Then i committed the changes and oushed them to the main branch
      - Lastly, i navigated to the "Actions" tab in GitHub and verified that the workflow is running  

  - Verify that the CI/CD pipeline correctly applies these changes to a Kubernetes cluster.

### <ins>Task 7: Manage Secrets and ConfigMaps</ins>

  - Use Kustomize to generate ConfigMaps and Secrets. Ensure sensitive data is handled securely.

  - Apply these configurations in your overlays for different environments.

      - I added a "generator.yaml" file to each of the different environments
   
          - Development

                apiVersion: kustomize.config.k8s.io/v1beta1
                kind: Kustomization
                
                resources:
                  - ../../base
                
                configMapGenerator:
                  - name: my-app-config
                    literals:
                        - ENVIRONMENT=development
                        - LOG_LEVEL=debug
                
                secretGenerator:
                  - name: my-app-secrets
                    files:
                        - db-password.txt
                        - api-key.txt
                    options:
                        disableNameSuffixHash: true    


    
          - Staging

                apiVersion: kustomize.config.k8s.io/v1beta1
                kind: Kustomization
                
                resources:
                  - ../../base
                
                configMapGenerator:
                  - name: my-app-config
                    literals:
                        - ENVIRONMENT=staging
                        - LOG_LEVEL=debug
                
                secretGenerator:
                  - name: my-app-secrets
                    files:
                        - db-password.txt
                        - api-key.txt
                    options:
                        disableNameSuffixHash: true
       


          - Production

                apiVersion: kustomize.config.k8s.io/v1beta1
                kind: Kustomization
                
                resources:
                  - ../../base
                
                configMapGenerator:
                  - name: my-app-config
                    literals:
                        - ENVIRONMENT=production
                        - LOG_LEVEL=debug
                
                secretGenerator:
                  - name: my-app-secrets
                    files:
                        - db-password.txt
                        - api-key.txt
                    options:
                        disableNameSuffixHash: true

