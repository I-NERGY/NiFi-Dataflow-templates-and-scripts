## More complex SQL DataFlow
Problem that is being solved in this data flow is data querying on a daily basis starting from 2019, its harmonization, conversion and publishing.

In this example we will go through attribute extraction, string manipulation, script execution, sql queries, data conversion and data publishing on both MongoDB and Kafka.
The flow consits of four parts which can be seen on the picture below. 

![image](https://user-images.githubusercontent.com/90190347/189384300-91d32e54-cd04-4b9b-a3a1-836ebffaea02.png)

## How does it work?
### Generating custom queries
The flow starts with ```GenerateFlowFile``` processor with predefined custom text in JSON format, representing the ```WHERE``` clausule for the SQL query. 

![image](https://user-images.githubusercontent.com/90190347/189371479-3ace7e2a-8717-4c08-8503-76b7d3fd62c9.png)

In this case we want to extract the current data for each device with a unique ID that has 3 phases ```l1, l2, l3``` on a daily basis, starting from 2019. Lets say for this example that the column name for selecting is named ```TagName``` and has values looking like ```id-phase```. The end result of the first process group would be a flowfile with an attribute for a ```WHERE``` clausule looking like ```TagName='w1-l1' or TagName='w1-l2' or TagName='w1-l3'``` where we select all three phases for a device ```w1```.

List of steps for the first group:
  1.  Generate the flow files like above
  2.  Split the JSON array data in flowfiles, each of them being one JSON object. We do that with ```SplitJson``` processor and setting ```JsonPath Expression``` to ```$.*```. 
  3.  Extract ```id``` and ```tags``` from data to attributes using the ```EvaluateJsonPath``` processor like on the picture below. 
  
  ![image](https://user-images.githubusercontent.com/90190347/189374893-48e1695e-0b18-40c4-be33-1f9febe6cde3.png)
  
  4.  Remove unwanted characters by replacing them with space using ```ReplaceText``` processor
  5.  Replace the ```,``` character with ```or```, giving us the correct ```WHERE``` clausule
  6.  Extract the whole content to an attribute called ```tag_query``` using the ```ExtractText```processor by adding a new property like on the picture below
 
  ![image](https://user-images.githubusercontent.com/90190347/189376504-0962dabf-6a89-47e9-865f-b7359e406139.png)
  
  The whole process group looks like this:
  
  ![image](https://user-images.githubusercontent.com/90190347/189376833-947b8f7f-d0c0-42df-a476-18407cb80fdf.png)


### Generating dates for each day and query using python
For each device, we want to query the current data for each day separately, so each flowfile has to have an attribute with ```end``` date, ```start``` date and ```tag_query```.

Regarding the script execution we will use the ```ExecuteStreamCommand``` processor which allows us to execute external commands on the instance while getting the input as the flowfile content and creating the new flowfile that contains the command results. In this case the ```Command Path``` will be ```/usr/bin/python3.9``` or whatever version of python is installed on the instance and ```Command Arguments``` that is ```/opt/scripts/generate_daily_dates.py```or whatever is the path to the script you want to execute.

In this case we have a ```generate_daily_dates``` that simply generates a JSON array where each object has a ```start``` and ```end``` that are 1 day apart. After they are generated we will split them in separate flow files in a similar fashion to the first process group and then extract then to an attribute. Every flow file coming from the first process group will keep its attributes for queries.

- Process group flow

![image](https://user-images.githubusercontent.com/90190347/189381407-7bb475ef-79d0-43b8-b0ed-43b2e5a869e8.png)

- ExecuteStreamCommand

![image](https://user-images.githubusercontent.com/90190347/189382485-9b87cd48-ab45-47c4-918f-53c80aef374d.png)

- SplitJson

![image](https://user-images.githubusercontent.com/90190347/189382522-6a324565-a74a-4eeb-b769-1bacabe3c1f9.png)
 
- EvaluateJsonPath

![image](https://user-images.githubusercontent.com/90190347/189382580-93b59395-d05d-428c-a22a-1420c6d468af.png)

- ModifyBytes

![image](https://user-images.githubusercontent.com/90190347/189382617-bca4e201-abcb-40d2-9eba-3bfe6ea35f20.png)

### Executing the queries
The next step is covered in the [Simple SQL example](https://github.com/I-NERGY/NiFi-Dataflow-templates-and-scripts/tree/developnikola/SQL/SimpleSql). The part that we are modifying is an actual select query 
```SELECT * FROM db.name WHERE (${tag_query}) and ([DateTime]>='${start}') and ([DateTime]<'${end}')``` where we are acessing the previously made attributes ```tag_query```, ```start``` and ```end```.

![image](https://user-images.githubusercontent.com/90190347/189383939-7928d389-4f14-4738-85c3-cadab3686675.png)

We use ```RouteOnAttribute``` processor that allows us to route flowfiles based on their attributes. In this case we are rejecting empty files by making a new route(property) called ```non-empty``` which contains ```${fileSize:gt(0)}```. In this way every flow file with bigger size than 0 bytes will be routed to ```non-empty``` route.

### Preprocessing and saving
In this last group we want to make sure that the raw collected data from MySQL is preprocessed by a harmonization script and then published to both Kafka and MongoDB. 
This example exists ir order to show how inputs and outputs work with ```ExecuteStreamCommand```.

The script in this case requires 2 inputs, one with raw data that will read from standard in and processed, and the id of the device passed as an argument. We can do that with ```ExecuteSteamCommand```, by passing the id attribute as an argument with ```Command Arguments``` looking like ```/opt/scripts/current_harmonizator.py;${ID}```, where the ```Argument delimiter``` is ```;```. The content of a flow file will be passed to a standard input by default.

![image](https://user-images.githubusercontent.com/90190347/189388542-43d8c609-7841-4ffe-aa8c-1634c8a3c9e0.png)

Now in the python script we can you the ```sys``` package to read the argument and data.

```python
self.id = sys.argv[1]
self.raw_data = sys.stdin.read()
```



