# CapStone Project: Implementing a Multi-Environment Application Deployment with Kustomize

## <ins>Hypothetical Use Case</ins>: You are tasked with deploying a web application in a Kubernetes environment. The application will have different configurations for development, staging and production environments. Your goal is to utilize Kustomize to manage these configurations efficiently and integrate the process into a CI/CD pipeline.

## <ins>Tasks</ins>: 

### <ins>Task 1: Set up your project</ins>

  - Create a new project directory named **kustomize-capstone**

      - I used the following command line to create the directory

            mkdir kustomize-capstone



  - Inside the directory, structure it with Kustomize conventions: **base/** and **overlays/** (subdirectories for dev, staging and prod).

      - I used the following command line to create all the directories and subdirectories

            mkdir base mkdir overlays mkdir overlays/dev mkdir overlays/staging mkdir overlays/prod



### <ins>Task 2: Initialize Git</ins>

  - Initialize a Git repository in your **kustomze-capstone** directory.

      - I navigated to the "source control" panel on vscode and selected "initialize repository"
   


  - Create a **.gitignore** file to exclude unnecessary files.

      - I used the "touch .gitignore" command line to create the .gitignore file and then input the following code into the file so as to exclude unnecessary files
   
            

### <ins>Task 3: Define Base Configuration</ins>

  - In the **base/** directory, define Kubernetes resources like Deployment, Service, etc., for your web application.

  - Create a **kustomize.yaml** file in **base/** to include these resources.

### <ins>Task 4: Create Environment-Specific Overlays:</ins>

  - In each subdirectory of **overlays/**, create a **kustomize.yaml** that customizes the base configuration for that environment.

  - Implement variations for each environment (e.g., different replica counts, resource limits, or environment variables).

### <ins>Task 5: Integrate with a CI/CD Pipleline</ins>

  - Choose a CI/CD platform (e.g., GitHub Actions, Jenkins)

  - Set up a pipeline that deploys your application using Kustomize. The pipeline should trigger on code changes.

### <ins>Task 6: Test the CI/CD Pipeline</ins>

  - Make changes in your Kustomize configurations and push to your repository

  - Verify that the CI/CD pipeline correctly applies these changes to a Kubernetes cluster.

### <ins>Task 7: Manage Secrets and ConfigMaps</ins>

  - Use Kustomize to generate ConfigMaps and Secrets. Ensure sensitive data is handled securely.

  - Apply these configurations in your overlays for different environments.
