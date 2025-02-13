# FabricForISVs

![image](https://github.com/user-attachments/assets/79ad4593-981d-4602-92c8-aff19ef7cd8c)


# Fabric PowerBI Embedded CI/CD sample implementation with GitHub actions

### Release branches

Branching strategy can be enforce through [Deployment Protection Rules](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-deployments/managing-environments-for-deployment). By doing so we can ensure that only *releases/\** branches can be published to customer environments.

Each *environment* will also define its own credentials and workspace name througn [Environment secrets](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-deployments/managing-environments-for-deployment#environment-secrets) to ensure least privilege is configured on the service principals.

### Example implementation of Option 4 with GitHub - [CI/CD for ISVs in Fabric (managing multiple customers/solutions)](https://learn.microsoft.com/en-us/fabric/cicd/manage-deployment#option-4---cicd-for-isvs-in-fabric-managing-multiple-customerssolutions)

![image](https://github.com/user-attachments/assets/826d4fc7-1894-4f7b-a7d0-c0b6aaa18006)


This option is different from the others. It's most relevant for Independent Software Vendors (ISV) who build SaaS applications for their customers on top of Fabric. ISVs usually have a separate workspace for each customer and can have as many as several hundred or thousands of workspaces. When the structure of the analytics provided to each customer is similar and out-of-the-box, we recommend having a centralized development and testing process that splits off to each customer only in the *Prod* stage.

1. A *build* pipeline is triggered to spin up a new *Build environment* and run unit tests for *dev* stage. When tests are complete, a *release* pipeline is triggered. This pipeline can upload the content to a *Build environment*, run scripts to change some of the configuration, adjust the configuration to *dev* stage, and then use Fabric’s [Update item definition](/rest/api/fabric/core/items/update-item) APIs to upload the files into the Workspace.
1. After this process is complete, including ingesting data and approval from release managers, the next *build* and *release* pipelines for *test* stage can kick off. This process is similar to that described in the first step. For *test* stage, other automated or manual tests might be required after the deployment, to validate the changes are ready to be release to *Prod* stage in high-quality.
1. Once all tests pass and the approval process is complete, the deployment to *Prod* customers can start. Each customer has its own release with its own parameters, so that its specific configuration and data connection can take place in the relevant customer’s workspace. The configuration change can happen through scripts in a *build* environment, or using APIs post deployment. All releases can happen in parallel as they aren't related nor dependent of each other.

#### When should you consider using option #4?

* You're an ISV building applications on top of Fabric.
* You're using different workspaces for each customer to manage the multi-tenancy of your application
* For more separation, or for specific tests for different customers, you might want to have multi-tenancy in earlier stages of *dev* or *test*. In that case, consider that with multi-tenancy the number of workspaces required grows significantly.
