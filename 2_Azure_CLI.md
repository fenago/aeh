### Lab Exercise: Exploring Azure CLI Read-Only Commands

#### Objective:
Familiarize with the Azure CLI by using various read-only commands to inspect and query Azure resources.

#### Requirements:
- Access to Azure Cloud Shell with an active Azure subscription.
- Basic familiarity with command-line interfaces.

#### Lab Steps:

1. **Introduction to Azure CLI**:
   - Start the Azure Cloud Shell.
   - Type `az` and press Enter to see the basic options and commands available in Azure CLI.

2. **Check Azure CLI Version**:
   - Run `az --version` to display the Azure CLI version and the versions of its dependencies. This is useful for troubleshooting and ensuring compatibility with scripts and features.

3. **List All Azure Commands**:
   - Execute `az find "az "` to see a list of all top-level commands available in the Azure CLI.

4. **Working with Azure Subscriptions**:
   - Run `az account list --output table` to display all the Azure subscriptions available to your account in a table format. This command helps you identify which subscriptions you can access.
   - Use `az account show` to display details of your currently active subscription.

5. **Exploring Resource Groups**:
   - Use `az group list --output table` to list all resource groups in your current subscription, outputting the result in a table format. This helps you see which resource groups are available.
   - To get more detailed information about a specific resource group, use `az group show --name [ResourceGroupName]` where `[ResourceGroupName]` is the name of one of your resource groups.

6. **Viewing Azure Services**:
   - Execute `az services list --output table` to see a list of available Azure services. This command may vary depending on CLI updates and available extensions.

7. **Inspecting Resources in a Resource Group**:
   - Use `az resource list --resource-group [ResourceGroupName] --output table` to list all the resources within a specific resource group.

8. **Querying Resource Properties**:
   - To get detailed properties of a specific resource, use `az resource show --resource-group [ResourceGroupName] --name [ResourceName] --resource-type [ResourceType]`. Replace placeholders with the actual names and type of the resource.

9. **Azure Locations and Regions**:
   - List all Azure locations using `az account list-locations --output table`. This is helpful to know where you can deploy resources.

10. **Check Azure Service Health**:
    - Run `az service-health list --output table` to check the health of Azure services across regions. This command gives insights into any ongoing issues or planned maintenance.

#### Conclusion:
This lab provides a hands-on introduction to navigating and querying Azure resources using Azure CLI in a non-destructive manner. Students will gain confidence in using command-line tools to explore and understand the structure and state of their Azure environment.

Encourage students to experiment with different `--output` formats like `json`, `table`, `yaml`, or `tsv` to better understand how data can be manipulated and visualized directly from the command line. This will prepare them well for more advanced tasks, such as managing Azure Event Hubs, in future labs.
