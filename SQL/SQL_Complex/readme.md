## More complex SQL DataFlow
Problem that is being solved in this data flow is data querying on a daily basis starting from 2019, its harmonization, conversion and publishing.

In this example we will go through attribute extraction, string manipulation, script execution, sql queries, data conversion and data publishing on both MongoDB and Kafka.
The flow consits of four parts which can be seen on the picture below. 

![image](https://user-images.githubusercontent.com/90190347/189370971-3f6cb76a-4353-4ac2-a31e-a0f63e86e8db.png)

## How does it work?
### Generating custom queries
The flow starts with ```GenerateFlowFile``` processor with predefined custom text in JSON format, representing the ```WHERE``` clausule for SQL query. 

![image](https://user-images.githubusercontent.com/90190347/189371479-3ace7e2a-8717-4c08-8503-76b7d3fd62c9.png)

