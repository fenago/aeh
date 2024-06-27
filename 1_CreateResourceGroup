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
