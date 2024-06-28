Certainly! Let's focus on creating a straightforward lab that introduces the basics of using a producer and a consumer with Azure Event Hubs. We'll use the default consumer group for simplicity in this initial lab, ensuring a clear and accessible starting point.

### Lab Guide: Introduction to Azure Event Hubs – Creating a Producer and a Consumer

#### Objective:
Familiarize yourself with Azure Event Hubs by developing a basic producer to send messages and a consumer to read these messages using the default consumer group.

#### Requirements:
- Azure subscription
- Azure Event Hub set up
- Python environment with the Azure Event Hub library installed
- Azure CLI installed (optional for setup verification)

#### Pre-Lab Setup:
Ensure you have an Event Hub instance ready. Use the Azure Portal or Azure CLI to confirm that your Event Hub and the default consumer group `$Default` are properly configured.

### Part 1: Creating the Producer

1. **Producer Code Setup**:
   - Open your IDE or a text editor and create a new Python file named `producer.py`.
   - Use the following script as your producer code. Make sure to replace placeholders with your actual Event Hub connection string and name:

```python
import asyncio
from azure.eventhub.aio import EventHubProducerClient
from azure.eventhub import EventData

async def run():
    producer = EventHubProducerClient.from_connection_string(
        "your_connection_string_here", eventhub_name="your_event_hub_name_here"
    )
    async with producer:
        event_data_batch = await producer.create_batch()
        event_data_batch.add(EventData("First event"))
        event_data_batch.add(EventData("Second event"))
        event_data_batch.add(EventData("Third event"))
        await producer.send_batch(event_data_batch)
        print("Events sent successfully.")

asyncio.run(run())
```

### Part 2: Creating the Consumer

1. **Consumer Code Setup**:
   - Create another Python file in the same directory named `consumer.py`.
   - Use this script for the consumer. It will use the default consumer group to read events:

```python
import asyncio
from azure.eventhub.aio import EventHubConsumerClient

async def on_event(partition_context, event):
    # Print the event data and the partition it was received from
    print(f"Received event: '{event.body_as_str()}' from partition: '{partition_context.partition_id}'")

async def main():
    client = EventHubConsumerClient.from_connection_string(
        "your_connection_string_here", consumer_group="$Default", eventhub_name="your_event_hub_name_here"
    )
    async with client:
        await client.receive(on_event=on_event, starting_position="-1")  # Read from the latest position

if __name__ == '__main__':
    asyncio.run(main())
```

### Part 3: Running the Code

1. **Run the Producer**:
   - Execute `producer.py` to send events to your Event Hub.
   - Confirm the print statement in the script outputs successfully.

2. **Run the Consumer**:
   - Execute `consumer.py` to start receiving events.
   - Observe the events printed by the consumer in real-time as they are processed.

### Validation:
Ensure that all events sent by the producer are being correctly received and printed by the consumer. This step confirms that your basic Event Hubs setup is functioning correctly.

### Conclusion:
This lab provides a foundational understanding of how to interact with Azure Event Hubs using a simple producer and consumer model. By the end of this lab, you should be able to send and receive messages through Azure Event Hubs using default settings.

### Next Steps:
In future labs, you will explore creating custom consumer groups and more complex event processing scenarios to enhance your understanding of Azure Event Hubs capabilities.


### Lab Guide: Expanding Azure Event Hubs Usage with Custom Consumer Groups

#### Objective:
Learn to create and utilize custom consumer groups within Azure Event Hubs to enable independent, parallel processing of event data streams.

#### Requirements:
- An existing Azure Event Hub instance (as used in the previous lab)
- Python environment with Azure Event Hub library installed
- Understanding of basic Event Hubs operations (from the previous lab)

### Part 1: Creating a Custom Consumer Group

#### Step 1: Create the Consumer Group
1. **Use Azure CLI**:
   - Open your command line interface and execute the following command to create a new consumer group named `custom-consumer-group`:
     ```bash
     az eventhubs eventhub consumer-group create --resource-group <YourResourceGroupName> --namespace-name <YourNamespaceName> --eventhub-name <YourEventHubName> --name custom-consumer-group
     ```
   - Replace `<YourResourceGroupName>`, `<YourNamespaceName>`, and `<YourEventHubName>` with your specific values.

2. **Verification**:
   - Confirm the creation of the consumer group by listing all consumer groups:
     ```bash
     az eventhubs eventhub consumer-group list --resource-group <YourResourceGroupName> --namespace-name <YourNamespaceName> --eventhub-name <YourEventHubName>
     ```
   - Ensure `custom-consumer-group` is listed among the consumer groups.

### Part 2: Modifying the Consumer Code

1. **Update Consumer Script**:
   - Open `consumer.py` from the previous lab.
   - Modify the script to connect using the new consumer group `custom-consumer-group` instead of `$Default`:
     ```python
     import asyncio
     from azure.eventhub.aio import EventHubConsumerClient

     async def on_event(partition_context, event):
         print(f"Received event: '{event.body_as_str()}' from partition: '{partition_context.partition_id}'")

     async def main():
         client = EventHubConsumerClient.from_connection_string(
             "your_connection_string_here", consumer_group="custom-consumer-group", eventhub_name="your_event_hub_name_here"
         )
         async with client:
             await client.receive(on_event=on_event, starting_position="-1")  # Read from the latest position

     if __name__ == '__main__':
         asyncio.run(main())
     ```

### Part 3: Running the Updated Consumer

1. **Execute the Consumer**:
   - Run `consumer.py` to start receiving events with the custom consumer group.
   - Observe that it operates independently and can run simultaneously with other consumers using different consumer groups.

### Validation:
Ensure that the consumer using the new `custom-consumer-group` receives all messages sent by the producer just like the default consumer. This demonstrates that multiple consumer groups can independently read the same data stream.

### Discussion:
- Discuss the benefits of using multiple consumer groups, such as fault isolation, load balancing, and tailored processing.
- Encourage experimenting with multiple consumers in different consumer groups simultaneously to observe how Event Hubs manages parallel consumption.

### Conclusion:
This lab teaches how to expand the capabilities of Azure Event Hubs by utilizing custom consumer groups for scalable and flexible event processing architectures. It provides a hands-on approach to understanding practical implementations in real-world scenarios.

### Next Steps:
- Encourage further experimentation with more complex consumer group setups.
- Suggest integrating other Azure services like Azure Functions or Azure Stream Analytics to process data streams from Event Hubs.


### Lab Guide: Advanced Azure Event Hubs Consumer Techniques

#### Objective:
Deepen your understanding of Azure Event Hubs by implementing advanced consumer features such as event checkpointing, error handling, and performance tuning to ensure robust, scalable, and efficient event processing.

#### Requirements:
- An existing Azure Event Hub instance with at least one custom consumer group (set up in previous labs)
- Python environment with Azure Event Hub library installed
- Basic familiarity with asynchronous programming in Python

### Part 1: Implementing Event Checkpointing

#### Why Checkpointing?
Checkpointing allows a consumer to record its position within the event stream. This is crucial for resilient processing, as it enables the consumer to resume reading from where it left off in case of a failure or restart, thus ensuring no events are missed or redundantly processed.

#### Step 1: Modify Consumer Code
1. **Update the Consumer Script**:
   - Open the `consumer.py` file and enhance it to support checkpointing after processing each batch of events.
   - Use the following updated script:
     ```python
     import asyncio
     from azure.eventhub.aio import EventHubConsumerClient
     from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore

     async def on_event(partition_context, event):
         print(f"Received event: '{event.body_as_str()}' from partition: '{partition_context.partition_id}'")
         # Checkpoint every time an event is processed
         await partition_context.update_checkpoint(event)

     async def main():
         # Blob storage connection string and container name for storing checkpoints
         blob_connection_str = 'your_blob_storage_connection_string_here'
         container_name = 'your_container_name_here'
         checkpoint_store = BlobCheckpointStore.from_connection_string(blob_connection_str, container_name)

         client = EventHubConsumerClient.from_connection_string(
             "your_connection_string_here", consumer_group="custom-consumer-group", eventhub_name="your_event_hub_name_here",
             checkpoint_store=checkpoint_store  # Pass the checkpoint store to the client
         )
         async with client:
             await client.receive(on_event=on_event, starting_position="-1")

     if __name__ == '__main__':
         asyncio.run(main())
     ```

### Part 2: Advanced Error Handling

#### Step 2: Enhance the Consumer for Robust Error Handling
- Modify the consumer to handle exceptions more effectively, ensuring it can recover from errors without losing its position in the event stream.
- Include error logging and more sophisticated exception management strategies.

### Part 3: Performance Optimization

#### Step 3: Optimize Consumer Performance
- Discuss techniques such as prefetching, batch processing, and tuning partition load balancing.
- Implement changes in the code to illustrate these optimizations:
  ```python
  client = EventHubConsumerClient(
      "your_connection_string_here", consumer_group="custom-consumer-group", eventhub_name="your_event_hub_name_here",
      prefetch_count=300  # Prefetch settings can be adjusted based on expected load and throughput
  )
  ```

### Validation:
- **Run the enhanced consumer** and monitor its output.
- **Test failure scenarios**: Manually stop the consumer to simulate a crash and restart it to validate that checkpointing works correctly.
- **Monitor performance metrics** in the Azure Portal to observe the effects of your optimizations.

### Conclusion:
This advanced lab builds on your existing knowledge and equips you with skills to implement sophisticated, resilient, and efficient consumers in Azure Event Hubs. By mastering these advanced techniques, you can ensure your event-driven architectures are robust and capable of handling large-scale, real-world workloads.

### Next Steps:
- Experiment with different settings for prefetch and checkpoint intervals to find the optimal configuration for your specific scenario.
- Explore integration with Azure Monitor and Application Insights for deeper telemetry and performance insights.

This structured approach to creating an advanced Azure Event Hubs consumer prepares you for complex and high-demand scenarios, ensuring you are well-equipped to manage event-driven systems effectively.
