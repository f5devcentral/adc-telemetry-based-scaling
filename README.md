ADC Telemetry-based Autoscaling
===============================
This solution, (see below) illustrates how F5's Automation Toolchain can integrate with third party analytics provider(s) to provide cloud-agnostic centralized application delivery monitoring and autoscaling.  

<img src="azure/images/arch.png" alt="Flowers">

The solution utilizes various third-party technologies/services along with F5’s automation toolchain including:
   
   - **F5 BIG-IP(s)** providing L4/L7 ADC Services
   - **F5 Declarative Onboarding**, (DO) and **Application Services 3 Extension**, (AS3) to deploy to configure BIG-IP application services
   - **F5 Telemetry Streaming**, (TS) to stream telemetry data to a third party analytics provider
   - **GitHub Actions** for workflow automation 
   - **Azure** public cloud for application hosting
   - **Hashicorp Terraform** and **Consul** for infrastructure provisioning, service discovery and event logging
   - **Third-party Analytics Provider**, (integrated with BIG-IP(s) via TS) for monitoring and alerting, (environment includes and ELK stack trial for testing/demo purposes)


## Deploying the Solution
Since the solution utilies Github Actions for orchestration it will be necessary to first [duplcate the repo](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-repository-on-github/duplicating-a-repository) into a Github account under your control.  After which you can clone the newly created repo locally to perform the initial application infrastructure deployment.


### GitHub Secrets
Create the following [GitHub secrets](https://docs.github.com/en/actions/reference/encrypted-secrets).  The secrets will be utilized by the actions workflow to securely update the Azure deployment. You will need to provide [Azure service prinicipal credentials](https://github.com/marketplace/actions/azure-login) as well as a [GitHub acces token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token) for your repository.

- GH_TOKEN   - *ex: ghp_mkqCzxBci0Sl3.......rY
- AZURE_CLIENT_ID   - *ex: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
- AZURE_CLIENT_SECRET   - *ex: XXXXXXXXXXXXXXXXXXXXXXXXXXX
- AZURE_SUBSCRIPTION_ID   - *ex: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
- AZURE_TENANT_ID   - *ex: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
- AZURE_CREDS  - Comination of the above in JSON format  -  *ex: {"clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",  "clientSecret": "XXXXXXXXXXXXXXXXXXXXXXXX", "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"}*

### Variables
The following variables, (*located in ./terraform/terraform.tfvars*) should be modified as necessary.

- location = The Azure region where the application infrastructure will be deployed   -  *default: "eastus"*
- github_owner = Github Account hosting the repository   -  *ex: "f5devcentral"*
- repo_path =     -  *ex: "/repos/f5devcentral/adc-telemetry-based-autoscaling/dispatches"*
- github_token =   -  *ex: "ghp_mkqCzxBci0Sl3.......rY"
- bigip_count =    -  *default: 1* 
- workload_count =    -  *default: 2* 
- bigip_min =    -  *default: 1* 
- bigip_max =    -  *default: 5* 
- workload_min =    -  *default: 2*  
- workload_max =    -  *default: 5* 
- scale_interval =  Interval, (in seconds) between scaling events.  Alerts fired within interval setting will fail. -  *default: 300*  

### Deployment Steps

