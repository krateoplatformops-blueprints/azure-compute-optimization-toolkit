# _Azure Compute Optimization Toolkit_ Blueprint
This blueprint installs a new page in your Krateo Composable Portal to show all the VMs in your Azure tenant and if they are overprovisoned/unused.

## What It Does
It configures the following for you:
- authenticated endpoints for the Azure REST API access
- continuous download and storage of FOCUS cost reports
- a new page with a table in the frontend that lists all compute instances
- optimization suggestions for unused/overprovisoned resources computed by OPA policies

## Install the Blueprint
### Requirements
In order to invoke the Azure REST API, you need to create credentials to pass to this blueprint to allow it to authenticate. The credentials are obtained by registering an application:
- Login to Azure portal
- Go to the `App registrations` page and create a New registration
- Set a name
- Choose `Accounts in this organizational directory only (Single tenant)` as the account type
- Visit the `Subscriptions` page, choose the subscription that you would like to fetch cost data from, copy the subscription id
- Go to the `Access control (IAM)` page under the selected subscription
- Click:
  - Add
  - Add role assignment
  - under the Role tab, select: `Reader`
  - under the Members tag, assign access to `User, group, or service principal` and select the created application as the member
  - review and assign the role.

To create the client credentials:
- go to the application page via the App registrations portal or from the search textbox
- in the Essentials section, copy Application (client) ID, and Directory (tenant) ID.
- go to `Certificates & secrets`, `Client secrets`, and `New client secret`
- remember to copy the client secret to a safe place (it will only be shown once).

Finally, configure the Azure FOCUS export: [see here](https://github.com/krateoplatformops-blueprints/azure-configuration-finops?tab=readme-ov-file#prepare-your-billing-report-in-focus).

### Installation
To install the Blueprint you will need to set all the variables gathered in your Azure environment in the requirements step:
```sh
helm install <name> azure-compute-optimization-toolkit \
  --repo https://marketplace.krateo.io \
  --namespace <krateo-namespace> \
  --version 1.0.0 \
  --set configuration.tenantId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
  --set configuration.clientId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
  --set configuration.clientSecret=xxxxxxxxxx \
  --set configuration.exporterConfig.additionalVariables.account_name=xxxxxxx \
  --set subscriptionId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
  --wait
```

The toolkit **must** be installed in the Krateo Namespace. If you install using Helm, then you must set `.Values.global.krateoNamespace` to the Krateo namespace (defaults to _krateo-namespace_).