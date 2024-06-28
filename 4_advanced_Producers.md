# Advanced Producers

### Part 1: Sending Events to Specific Partitions

#### Use Case:
**Objective:** Demonstrate how to send events to a specific partition within an Event Hub. This approach is beneficial when there's a need for preserving the order of events or grouping related events, which is crucial for scenarios like transaction processing where order matters.

#### Code Setup:
Create a file named `specific_partition_producer.py` and input the following Python script with detailed comments:

```python
import asyncio
from azure.eventhub.aio import EventHubProducerClient
from azure.eventhub import EventData

# Azure Event Hub connection string and the name of the Event Hub
connection_str = 'Your Connection String Here'
eventhub_name = 'Your Event Hub Name'

async def run():
    # Create an asynchronous Event Hub Producer Client
    producer = EventHubProducerClient.from_connection_string(conn_str=connection_str, eventhub_name=eventhub_name)
    
    async with producer:
        # Create a batch with a specific partition ID
        event_data_batch = await producer.create_batch(partition_id='0')
        # Add a simple event to the batch
        event_data_batch.add(EventData('Specific partition event'))
        # Asynchronously send the batch to the specified partition
        await producer.send_batch(event_data_batch)
        print("Event sent to specific partition.")

asyncio.run(run())
```

#### Validation in Azure Portal:
- **Metric to Monitor:** Go to your Event Hub instance in the Azure Portal, navigate to `Metrics`, select the metric **"Incoming Messages"**, and filter by the specific partition. This will show you the number of messages sent to that partition.
- **Documentation:** Explore more about [Azure Event Hubs Partitioning](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-features#partitions).

### Part 2: Synchronous and Asynchronous Event Publishing

#### Use Case:
**Objective:** Understand the differences between synchronous and asynchronous event publishing. Synchronous publishing waits for confirmation that events have been committed, which is critical for applications requiring immediate confirmation of data receipt. Asynchronous publishing, on the other hand, allows for higher throughput by not waiting for this confirmation.

#### Code Setup:
Create `sync_async_producer.py`:

```python
from azure.eventhub import EventHubProducerClient, EventData
import time

# Azure Event Hub connection string and the name of the Event Hub
connection_str = 'Your Connection String Here'
eventhub_name = 'Your Event Hub Name'

def run_sync():
    # Create a synchronous Event Hub Producer Client
    producer = EventHubProducerClient.from_connection_string(connection_str, eventhub_name=eventhub_name)
    with producer:
        # Create a batch
        event_data_batch = producer.create_batch()
        # Add a simple event to the batch
        event_data_batch.add(EventData('Synchronous event'))
        # Synchronously send the batch
        producer.send_batch(event_data_batch)
        print("Synchronously sent event.")

def run_async():
    import asyncio
    async def send_async():
        # Create an asynchronous Event Hub Producer Client
        producer = EventHubProducerClient.from_connection_string(connection_str, eventhub_name=eventhub_name)
        async with producer:
            # Create a batch
            event_data_batch = await producer.create_batch()
            # Add a simple event to the batch
            event_data_batch.add(EventData('Asynchronous event'))
            # Asynchronously send the batch
            await producer.send_batch(event_data_batch)
            print("Asynchronously sent event.")
    asyncio.run(send_async())

# Measure sync time
start_time = time.time()
run_sync()
print(f"Synchronous sending time: {time.time() - start_time}s")

# Measure async time
start_time = time.time()
run_async()
print(f"Asynchronous sending time: {time.time() - start_time}s")
```

#### Validation in Azure Portal:
- **Metric to Monitor:** In the Azure Portal under `Metrics`, monitor **"Outgoing Messages"** to verify that both synchronous and asynchronous messages are being sent.
- **Documentation:** Read more about [synchronous and asynchronous operations in Azure Event Hubs](https://docs.microsoft.com/en-us/azure/event-hubs/).

### Part 3: Implementing Publisher Policies

#### Use Case:
**Objective:** Secure your Event Hub by configuring and enforcing Publisher Policies. This mechanism ensures that only registered publisher IDs can send data to your Event Hub, which is crucial for environments where you need to control data ingress strictly and validate the sources of incoming data.

#### Pre-Lab Setup:
**Configure Publisher ID in Azure Portal:**

1. **Login to the Azure Portal** at https://portal.azure.com.
2. Navigate to **Event Hubs** and select the specific Event Hub Namespace you are working with.
3. Under **Settings**, select **Shared access policies**.
4. Here, choose an existing policy or create a new one by clicking **+ Add**.
5. In the policy settings, ensure that **Send** is ticked if it isn't already, as publishers need permission to send events.
6. **Save** the policy if you made any changes or created a new one.
7. For setting up a Publisher ID, you will actually use this in the connection string rather than setting it in the portal. The Publisher ID is appended to the connection string as `PublisherId=YourPublisherId`.

**Note**: The Azure Portal does not explicitly configure a "Publisher ID" per publisher; rather, you assign a unique identifier to each publisher when constructing your connection string.

#### Code Setup:
Create a file named `publisher_policy_producer.py` with detailed instructions in comments:

```python
from azure.eventhub import EventHubProducerClient, EventData

# Define your Event Hub connection string and Event Hub name
# The Publisher ID is appended to the connection string. It is not a native Azure setting but a parameter you define for security purposes.
connection_str = 'Your Connection String Here'
eventhub_name = 'YourEventHubName'

def run():
    # Create an Event Hub Producer Client using the connection string that includes the Publisher ID
    producer = EventHubProducerClient.from_connection_string(connection_str, eventhub_name=eventhub_name)
    with producer:
        # Create a batch and add a single event
        event_data_batch = producer.create_batch()
        event_data_batch.add(EventData('Event with publisher policy'))
        # Send the batch to the Event Hub
        producer.send_batch(event_data_batch)
        print("Event sent with publisher policy.")

run()
```

#### Validation in Azure Portal:
- **Metric to Monitor:** Navigate to your Event Hub instance in the Azure Portal, click on **Metrics**, and select the metric **"Incoming Messages"**.
- **Validate:** Ensure messages are being received and also check **"Failed Requests"** for any unauthorized attempts which could indicate improper use of Publisher IDs.  Try with a bad ID to validate.

### Part 4

1. **Verify Event Hub Configuration**:
   - Go to your Event Hub Namespace.
   - Click on **Event Hubs** under **Entities** and select your Event Hub.
   - Under **Entities**, verify that the number of partitions is at least two. This is necessary to demonstrate dynamic routing effectively.

### Part 5: Implementing Dynamic Event Routing
Dynamic event routing can be accomplished by specifying the partition key or partition ID when sending events. This part of the lab will demonstrate both methods.

#### Code Setup:
1. **Create a Python Script**:
   - Open your preferred IDE or text editor and create a new file named `dynamic_routing_producer.py`.

2. **Script Content**:
   - Insert the following Python code. This script demonstrates sending messages to specific partitions based on predefined logic:
   ```python
   import asyncio
   from azure.eventhub.aio import EventHubProducerClient
   from azure.eventhub import EventData

   connection_str = 'Your Connection String Here'
   eventhub_name = 'Your Event Hub Name'

   async def run():
       producer = EventHubProducerClient.from_connection_string(connection_str, eventhub_name=eventhub_name)
       async with producer:
           # Sending events to different partitions
           for partition_id in ['0', '1']:  # Make sure these partition IDs exist in your Event Hub
               event_data_batch = await producer.create_batch(partition_id=partition_id)
               event_data_batch.add(EventData(f'Message for partition {partition_id}'))
               await producer.send_batch(event_data_batch)
               print(f"Batch sent to partition {partition_id}")

   asyncio.run(run())
   ```

   - **Comments**: This script uses asynchronous calls to send batches of events to specific partitions. Modify `connection_str` and `eventhub_name` with your actual Event Hub details.

### Part 3: Validation in Azure Portal
1. **Monitor Event Distribution**:
   - Navigate to the Azure Portal and open your Event Hub instance.
   - Go to **Metrics** under the monitoring section.
   - Set the metric to **"Incoming Messages"**.
   - Use the **"Split by"** dimension and select **"Partition Id"** to see the distribution of messages across partitions.

### Part 4: Conclusion and Further Exploration
This lab demonstrates the basic capability to route events dynamically to specific partitions in Azure Event Hubs. Explore further by varying the logic for partition selection, such as implementing hash-based or round-robin partitioning in your script.

#### Additional Resources:
- Azure Event Hubs Documentation: [Azure Event Hubs Documentation](https://docs.microsoft.com/en-us/azure/event-hubs/)
- Dynamic Routing Concepts: [Partitioning in Azure Event Hubs](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-features#partitions)

