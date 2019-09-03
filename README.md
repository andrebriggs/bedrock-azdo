# How to automate Bedrock GitOps set up using official AzDO CLI

## Pre-reqs
* Have an existing AzDO organization
    * Or create an org via Azure Marketplace (can probably be automated)
* Install `az devops cli` extension to `az`
* Install `hub` if wanting GitHub repos
    * Out of scope for the moment but PAT and ORg would be needed too 

## Inputs we need
* AzDO Project Name
* AzDO Org Name
* AzDO PAT for Org
* HLD Repo Name
* Manifest Repo Name
* ACR Name (Resource Group + Sub Id)
* Service Principal??
* Org Email addresses of people we want to give access to AzDO project

## How do I set who has access to the AzDO Project?
* Optionally create a "team" and associate it with the project
  * `az devops team create`
* Optionally add users (via email address) to the "team"
  * `az devops user add`

## How do I create Git repos?
* Create Azure git repos
  * `az repos create --name AppCode -p Rocket2019 | jq webUrl`
* Create GitHub repos
    ```
    git init
    ...
    hub create
    git push
    ```

# How do I connect other cloud resoruces to the AzDO project?
* Create AzureRM service endpoint
  * ```az devops service-endpoint azurerm create```

* Set up connection to existing ACR
  * ```Use REST API```

* Optionally create GitHub service connection to existing GitHub repos
  * `az devops service-endpoint github create`

# How do I create variables that pipelines can access?
* Create a variable group for the pipelines
  * `az pipelines variable-group create --name git_ops_vars --variables city="san francisco" zipcode=94105 --authorize true -p Rocket2019`

* Can't seem to set secret in variable group or add new var to group. Use REST api instead
  * `az pipelines variable create --name access_token --value foo --secret`

# How do I connect the git repos to the AzDO project to make pipelines?    
* I prefer we locally generated `azure-pipeline.yml` files along with all the other scaffolded files we want and commit/push into the remote Git repos.

* After we push the `azure-pipeline.yml` files and we can create a pipeline by associated the Git repo to the yaml file: 
  * `az pipelines create --name TurkeyPipeline -p Rocket2019 --repository dev.azure.com/abrig/Rocket2019/_git/AppCode --branch master --yaml-path azure-pipeline.yml`        

* **IF MONOREPO**: After creating the pipeline we will want to update the names of the build definition.Will have to use REST API
  * `az pipelines build definition list`



