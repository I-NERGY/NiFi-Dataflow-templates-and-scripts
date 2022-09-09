## More complex SQL DataFlow
Problem that is being solved in this data flow is data querying on a daily basis starting from 2019, its harmonization, conversion and publishing.

In this example we will go through attribute extraction, string manipulation, script execution, sql queries, data conversion and data publishing on both MongoDB and Kafka.
The flow consits of four parts which can be seen on the picture below. 

![image](https://user-images.githubusercontent.com/90190347/189370971-3f6cb76a-4353-4ac2-a31e-a0f63e86e8db.png)

## How does it work?
### Generating custom queries
The flow starts with ```GenerateFlowFile``` processor with predefined custom text in JSON format, representing the ```WHERE``` clausule for the SQL query. 

![image](https://user-images.githubusercontent.com/90190347/189371479-3ace7e2a-8717-4c08-8503-76b7d3fd62c9.png)

In this case we want to extract the current data for each device with a unique ID that has 3 phases ```l1, l2, l3``` on a daily basis, starting from 2019. Lets say for this example that the column name for selecting is named ```TagName``` and has values looking like ```id-phase```. The end result of the first process group would be a flowfile with an attribute for a ```WHERE``` clausule looking like ```TagName='w1-l1' or TagName='w1-l2' or TagName='w1-l3'``` where we select all three phases for a device ```w1```.

List of steps for the first group:
⋅⋅⋅ Test
⋅⋅⋅ Test
First thing that is done, is spliting the JSON array data in flowfiles, each of them being one JSON object. We do that with ```SplitJson``` processor and setting ```JsonPath Expression``` to ```$.*```. 
