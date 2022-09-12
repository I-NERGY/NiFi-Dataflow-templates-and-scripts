## Read data in XML format, parse it into JSON, and send  in Mongo and Kafka
A template shows how to get XML files from the SFTP server and prepare them for storage in JSON format.
![image](https://user-images.githubusercontent.com/84182102/189157668-816f35a3-a429-41d8-ad08-e0e278e61250.png)


## How it works?
To receive files from SFTP  in the NiFi Cluster environment it is necessary to correctly specify the properties for SFTP such as hostname, port, username, password, as well as the exact remote path to the directory from which the files are taken. Required properties should be entered both for ```ListSFTP``` and ```FetchSFTP``` processors. The ```sftp_gathering``` Process Group with mentioned processors is below.

![sftp_gathering](https://user-images.githubusercontent.com/84182102/189149780-49a7c601-839b-4a36-abee-556259322a12.JPG)

For ```ListSFTP``` mandatory fields are hostname, port, username and remote path. The ```List Strategy``` option refers to how to determine new entities. The default option is ```Tracking Timestamps```.
![listsftp](https://user-images.githubusercontent.com/84182102/189674459-f94b671e-c6ea-4615-95f5-6cf2f798f211.JPG)

In a NiFi cluster environment, it should be also ensured that nodes do not receive the same files multiple times. This is achieved by setting ```Primary node``` for the ```Execution``` in the ```Scheduling``` tab. 
![listsftpschedule](https://user-images.githubusercontent.com/84182102/189674620-2dae0f65-8c2f-4ebb-a8e1-11e0e3d1f312.JPG)

The fields for hostname, port and username are important for ```FetchList``` processor. 

For conversion  XML file use  ```TransformXML``` processor and specify provided ```xmltojson.xslt``` or ```xml-to-json.xsl``` stylesheet  as ```XSLT file name```. It converts any XML document to JSON format.

![transform](https://user-images.githubusercontent.com/84182102/189159159-cab3a3b5-64a4-4461-b7ec-78c95688375b.JPG)

After conversion is completed, the data is ready to be published to MongoDB. The ```PutMongoRecord``` processor is used for Mongo.
Mongo URI should be in format: ```mongodb://${mongo_user}:${mongo_password}@${mongo_ip}/```. The local group variables can be used to set these up. ```PutMongoRecord``` requires a ```Record Reader```. Here is used ```JsonTreeReader``` with default properties.
![image](https://user-images.githubusercontent.com/84182102/189681415-d12098c2-528b-4b0e-a13d-dc22fb53320f.png)

For Kafka  ```PublishKafka_2_6``` processor can be used. It is necessary to fill in certain fields as in the picture below.
![image](https://user-images.githubusercontent.com/84182102/189682664-5da5e462-0ca9-4c73-b97b-fd7fd04678a1.png)

For secured Kafka, it is necessary to create a new service ```StandardRestrictedSSLContextService``` with included Kafka certificate information. ```StandardRestrictedSSLContextService```  is  property for ```SSL Context Service``` in the ```PublishKafka``` processor. A certificate for Kafka  should be provided in JKS format.

![sslservicee](https://user-images.githubusercontent.com/84182102/189151327-8e07fcbe-23e7-4352-b658-ea68e7f4c578.JPG)
