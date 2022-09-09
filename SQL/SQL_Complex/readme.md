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

- Process group flow ![image](https://user-images.githubusercontent.com/90190347/189381407-7bb475ef-79d0-43b8-b0ed-43b2e5a869e8.png)

- ExecuteStreamCommand ![image](https://user-images.githubusercontent.com/90190347/189382485-9b87cd48-ab45-47c4-918f-53c80aef374d.png)

- SplitJson ![image](https://user-images.githubusercontent.com/90190347/189382522-6a324565-a74a-4eeb-b769-1bacabe3c1f9.png)
 
- EvaluateJsonPath ![image](https://user-images.githubusercontent.com/90190347/189382580-93b59395-d05d-428c-a22a-1420c6d468af.png)

- ModifyBytes ![image](https://user-images.githubusercontent.com/90190347/189382617-bca4e201-abcb-40d2-9eba-3bfe6ea35f20.png)





