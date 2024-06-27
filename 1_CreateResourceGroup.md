### Pre-Lab 1: Creating a Resource Group in Azure

#### Objectives:
- To create a new resource group in Azure that will be used to organize all resources related to Azure Event Hub.

#### Prerequisites:
- An active Azure subscription.
- Access to the Azure portal with at least contributor permissions.

#### Step-by-Step Instructions:
1. **Sign in to the Azure Portal**:
   - Go to [https://portal.azure.com](https://portal.azure.com) and sign in with your credentials.

2. **Navigate to Resource Groups**:
   - On the Azure portal dashboard, select "Resource groups" from the sidebar or search for "Resource groups" in the top search bar.

3. **Create a New Resource Group**:
   - Click on "+ Create" to start the process of creating a new resource group.
   - Choose the subscription in which you want to create the resource group.
   - Enter a name for your resource group, such as "GroupX-RG". (replace X with the name of your group to ensure there are no duplicates)
   - Select the region where you want this resource group to be located. It's a good practice to choose a region closest to your users for better performance.
   - Click "Review + create" to verify the details, then click "Create" to establish the resource group.

### Creating a User with Admin Capabilities over Azure Event Hub

#### Objectives:
- To create a user account with full administrative privileges over Azure Event Hub within the newly created resource group.

#### Prerequisites:
- A resource group in Azure.
- Azure Active Directory (Azure AD) role to create users and assign roles.

#### Step-by-Step Instructions:
1. **Create a New User in Azure Active Directory**:
   - In the Azure portal, navigate to Azure Active Directory.
   - Select "Users" and then "+ New user".
   - Fill out the form with the user details. Set the "User name" and "Name".
   - Choose the directory role as "User" (you will assign specific permissions to Azure Event Hub later).
   - Click "Create" to add the user to your directory.

2. **Assign Role to User for Azure Event Hub**:
   - Navigate to "Subscriptions" and select your subscription.
   - Under the "Access control (IAM)" tab, click "+ Add" and select "Add role assignment".
   - Choose the "Owner" or "Contributor" role to give full admin capabilities to the user for managing Azure Event Hub.
   - Search for the user you created by typing their name in the "Select" box.
   - Select the user and click "Save" to assign the role.

By following these steps, you will have successfully created a resource group in Azure and a user with full administrative capabilities over Azure Event Hub. Make sure to adjust permissions according to your security policies and organizational requirements.

## Continue and add for RG
Here are the step-by-step instructions to add a user to a resource group and assign full administrative privileges over any Azure Event Hub (AEH) that will be created and managed within this resource group:

1. **Add User to the Resource Group**:
   - Sign in to the Azure Portal at [https://portal.azure.com](https://portal.azure.com).
   - Navigate to "Resource groups" and select the resource group "GroupX-RG".
   - Click on "Access control (IAM)".
   - Click on "+ Add" and select "Add role assignment".
   - Choose the role "Contributor" to give administrative access over resources within the group Under Privileged administrator roles tab.  (you can also choose Owner).  
   - Click on Members and search for the user by name or email address after clicking +Select members.
   - Select the user from the search results and click "Save" to grant them access to the resource group.

2. **Assign Full Admin Capabilities Over Azure Event Hub**:
   - Within the same "GroupX-RG" resource group, ensure that any Azure Event Hub created inherits permissions from this group.
   - Verify that the "Contributor" role is sufficient for full administrative capabilities over the Azure Event Hub. If not, consider using the "Owner" role for broader access.
   - If creating a new Event Hub, navigate to the Azure Event Hub service within the resource group and confirm that the inherited permissions apply.

These steps will ensure that the specified user has full administrative capabilities over any Azure Event Hub created within "Group73-RG". This setup facilitates management and oversight of resources effectively.

