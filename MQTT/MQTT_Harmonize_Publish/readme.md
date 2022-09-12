## Data collection with MQTT, processing and publishing
Dataflow that collects data from multiple MQTT topics, processes it and publishes it to MongoDB and Kafka.

![image](https://user-images.githubusercontent.com/90190347/189676568-3aa1c47b-b7df-4743-b6e9-6874cb80e2ee.png)

### How does it work

![image](https://user-images.githubusercontent.com/90190347/189677610-4b06a63b-f556-4a68-9bc3-b38d83b43322.png)

Starting from the first process group, we use ```ConsumeMQTT``` processors. Configuration consists of the ```Broker URI``` set to ```tcp://ip_address```, username, password fields and ```Topic Filter``` field set to desired topic. In this case we want to collect the data from 3 different topics and to upload them to 3 different collections both on MongoDB and Kafka. ```ConsumeMQTT``` processor configuration looks like this:

![image](https://user-images.githubusercontent.com/90190347/189677853-53bb299e-5fbc-417e-b47d-bb1c299a9abe.png)

![image](https://user-images.githubusercontent.com/90190347/189677887-7cf6dad3-9543-4ed9-b626-1f7e99d89104.png)

Next, we add a collection name attribute to the data comming from MQTT using ```UpdateAttribute``` processor.

![image](https://user-images.githubusercontent.com/90190347/189678237-81a0e76c-1c6a-48f1-866a-5a062de6d288.png)

After that we change the flow file name using [NiFi's expression Language](https://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html).

![image](https://user-images.githubusercontent.com/90190347/189678530-f254315f-dfe4-4d40-ac4c-df659b90ccb0.png)

The rest of the flow is coevered in [SQL Comples](https://github.com/I-NERGY/NiFi-Dataflow-templates-and-scripts/tree/developnikola/SQL/SQL_Complex), including custom script execution with parameters and publishing the data on MongoDB and Kafka.
