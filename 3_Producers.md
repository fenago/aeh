### Lab Exercise: Sending Messages to Azure Event Hubs using Python

#### Objective:
Learn how to create a Python producer to send messages to Azure Event Hubs, and then use the Azure Portal to inspect and analyze the messages.

#### Requirements:
- An active Azure subscription.
- Azure CLI installed or access to Azure Cloud Shell.
- Python installed on your local machine.
- Access to the Azure Portal.

#### Lab Steps:

1. **Setup Azure Event Hub**:
   - Log into the Azure Portal.
   - Navigate to "Create a resource" > "Event Hubs" and create a new namespace.
   - Inside the namespace, create an Event Hub.

2. **Obtain Connection String**:
   - In the Azure Portal, navigate to your Event Hub namespace.
   - Click on "Shared access policies" on the left menu.
   - Click on "RootManageSharedAccessKey".  (that is the default name - yours may be named differently)
   - Copy the "Primary Connection String" from the policy details.

3. **Install Required Python Package**:
   - Open a command prompt or terminal.
   - Ensure Python is installed by running `python --version`.
   - Install the Azure Event Hub package with the command:
     ```
     pip install azure-eventhub
     ```

4. **Create Python Producer Script**:
   - Open your favorite Python editor (e.g., Visual Studio Code).
   - Create a new file named `send.py`.
   - Paste the following code, replacing `EVENT_HUB_CONNECTION_STR` with your copied connection string, and `EVENT_HUB_NAME` with the name of your event hub:

     ```python
     import asyncio
     from azure.eventhub import EventData
     from azure.eventhub.aio import EventHubProducerClient

     EVENT_HUB_CONNECTION_STR = "your_connection_string_here"
     EVENT_HUB_NAME = "your_event_hub_name_here"

     async def run():
         producer = EventHubProducerClient.from_connection_string(
             conn_str=EVENT_HUB_CONNECTION_STR, eventhub_name=EVENT_HUB_NAME
         )
         async with producer:
             event_data_batch = await producer.create_batch()
             event_data_batch.add(EventData("First event"))
             event_data_batch.add(EventData("Second event"))
             event_data_batch.add(EventData("Third event"))
             await producer.send_batch(event_data_batch)

     asyncio.run(run())
     ```

5. **Run the Python Script**:
   - Execute the script by running:
     ```
     python send.py
     ```

It will not provide any feedback if it is successful.

6. **Cleanup Resources**:
   - To avoid unnecessary charges, remember to clean up your resources by deleting the Event Hub namespace or stopping the Stream Analytics jobs when done.

#### Conclusion:
This lab guides you through creating a Python application that sends messages to Azure Event Hubs and then reviewing these messages within the Azure Portal. This exercise helps you understand event ingestion and real-time data streaming in Azure.

### Additional Notes:
- Always secure your connection strings and consider using Managed Identities for Azure services in production environments to enhance security.
- Explore different pricing tiers and features of Azure Event Hubs according to your needs.

By following these steps, you'll gain practical experience with Azure Event Hubs and Python programming, providing a strong foundation for building event-driven applications.

# CONTINUE

To monitor your Azure Event Hubs using Azure Monitor effectively, you can leverage a combination of metrics and logs to gain insights into the performance and health of your Event Hub. Here’s a step-by-step guide on how to set up monitoring and which metrics to focus on:

### Step 1: Enable Diagnostics Settings for Event Hubs

1. **Navigate to Your Event Hub Namespace** in the Azure Portal.
2. **Click on Diagnostics settings** under the **Monitoring** section.
3. **Click on + Add diagnostic setting** if there's no existing setting.
4. **Name your diagnostic setting** (e.g., "EventHubDiagnostics").
5. **Select the logs and metrics** you want to collect. For basic monitoring, you can enable:
   - **Logs:**
     - `ArchiveLogs`: Captures detailed operation logs.
     - `OperationalLogs`: Contains logs about operations performed in the Event Hub.
   - **Metrics:**
     - All metrics for comprehensive monitoring.
6. **Choose the destination** for the logs and metrics:
   - You can send them to a **Log Analytics workspace**, an **Event Hub** (for processing by another application or Azure Stream Analytics), or **Azure Storage** (for archiving).
7. **Save** the diagnostics setting.

### Step 2: Access Metrics through Azure Monitor

1. **Go to the Azure Monitor** from the Azure Portal home page.
2. **Select Metrics** from the left panel.
3. **Set the scope** to your Event Hub namespace.
4. **Add the metrics** to visualize. Here are several important metrics to monitor:

   - **Incoming Messages**: Shows the number of incoming messages. It's crucial for understanding the load and ensuring that data is flowing into your Event Hub.
   - **Outgoing Messages**: Useful to monitor how many messages are being consumed. It helps verify that downstream processing is keeping up with the input.
   - **Incoming Bytes and Outgoing Bytes**: Measure the amount of data entering and leaving your Event Hub. These metrics help in assessing throughput and potential bottlenecks.
   - **Capture Queue Count**: Shows the number of unconsumed messages waiting in the queue. A high number can indicate issues with downstream consumers.
   - **Throttled Requests**: If non-zero, it indicates that you are hitting the throughput limits of your pricing tier, which might require scaling up or optimizing data flows.
   - **Server Errors and User Errors**: Help in identifying issues that need to be addressed. Server errors are particularly critical as they might indicate Azure-side issues.

### Step 3: Set Up Alerts

1. **Go back to Azure Monitor**, and select **Alerts**.
2. **Click on + New alert rule**.
3. **Select the Event Hub namespace** as the resource.
4. **Add a condition** based on the metrics above. For example, set an alert for:
   - `Throttled Requests > 0`: To get notified if your Event Hub is being throttled.
   - `Server Errors > 0`: To react to potential service-related issues.
5. **Define the action group** (who gets notified and how).
6. **Configure the alert details**, name it, and set the severity level.
7. **Create the alert rule**.

By following these steps, you'll have a robust monitoring setup that helps you keep track of the health, performance, and usage of your Azure Event Hub. This setup enables proactive management and troubleshooting, ensuring that your event-driven architecture runs smoothly.

To utilize Log Analytics for monitoring your Azure Event Hubs, you must configure your Event Hubs to send logs and metrics to a Log Analytics workspace. Here's how to enable and configure this:

### Step 1: Create or Select a Log Analytics Workspace

If you don’t already have a Log Analytics workspace, you'll need to create one:

1. **Navigate to the Azure Portal** (https://portal.azure.com).
2. Click on **Create a resource**.
3. Search for and select **Log Analytics**.
4. Click **Create**.
5. Fill in the details:
   - **Subscription**: Choose the subscription where you want to place the workspace.
   - **Resource Group**: Select an existing resource group or create a new one.
   - **Name**: Give your workspace a unique name.
   - **Region**: Choose the region that best fits your requirements (ideally, the same region as your Event Hubs for data residency and network latency considerations).
6. Click **Review + Create**, then **Create** once validation passes.

### Step 2: Enable Diagnostics Settings in Event Hubs to Send Data to Log Analytics

1. **Go to your Event Hub Namespace** in the Azure Portal.
2. Under the **Monitoring** section, click on **Diagnostics settings**.
3. Click on **+ Add diagnostic setting**.
4. Enter a name for the diagnostic setting.
5. Under **Logs and Metrics**:
   - Select the types of logs and metrics you want to send to Log Analytics. It's recommended to enable at least:
     - **OperationalLogs**: Contains logs about operations performed on your Event Hubs.
     - **ArchiveLogs**: Provides detailed operation logs.
     - You can also select **Metrics** if you want to analyze performance metrics in your Log Analytics workspace.
6. In the **Destination details**:
   - Check the box for **Send to Log Analytics workspace**.
   - Select the **Subscription** and **Log Analytics workspace** you created or identified earlier.
7. Click **Save**.

### Step 3: Query Logs and Metrics in Log Analytics

Once your logs and metrics are being sent to the Log Analytics workspace, you can use the powerful Kusto Query Language (KQL) to analyze and visualize the data:

1. **Navigate to your Log Analytics workspace** in the Azure Portal.
2. Click on **Logs** in the workspace’s menu.
3. Start writing queries to explore your data. For example, to see operational logs:
   ```kusto
   EventHubOperationalLogs
   | where TimeGenerated > ago(1d)
   | summarize count() by OperationName, ResultType
   | order by count_ desc
   ```
   This query summarizes the operations performed on your Event Hubs, grouped by operation type and result, for the last day.

### Monitoring Setup and Tips

- Regularly check your logs for anomalies or issues, especially "Server Errors" which indicate problems on Azure's side.
- Use **Alerts** in Azure Monitor to get real-time notifications based on specific metrics or log patterns you define in Log Analytics.
- If handling sensitive data, ensure compliance with data governance and privacy standards when configuring diagnostics and monitoring tools.

By following these steps, you'll enable comprehensive monitoring of your Azure Event Hubs, leveraging the depth of analysis provided by Azure Monitor and Log Analytics. This setup will help you maintain the performance, health, and reliability of your event-driven architectures.
