## Read data in CSV format, parse it into JSON and send  in Mongo and Kafka
The template shows how to get CSV files from the SFTP server and prepare them for storage in JSON format. 
![1](https://user-images.githubusercontent.com/84182102/189149386-4d6d3a8c-faac-4f93-8b46-6d40b53f5e74.JPG)


## How it works?
To receive files from SFTP  in the NiFi Cluster environment it is necessary to correctly specify the properties for SFTP such as hostname, port, username, password, as well as the exact remote path to the directory from which the files are taken. Required properties should be entered both for ListSFTP and FetchSFTP processors. The sftp_gathering Process Group with mentioned processors is below.

![sftp_gathering](https://user-images.githubusercontent.com/84182102/189149780-49a7c601-839b-4a36-abee-556259322a12.JPG)
 
In a NiFi cluster environment, it should be ensured that nodes do not receive the same files multiple times. This is achieved by setting "Primary node" for the Execution for ListSFTP processor in the Scheduling tab. 

After receiving CSV files from SFTP, files are further sent to the SplitText processor to be split if they are larger than the defined property. 

With ConvertRecord, the split files are converted to JSON and stored in Mongo and Kafka. The comma operator(,) is a default separator. If there is a need to change that option it is necessary to customize the CSVReader service for that processor.
![convertrecord](https://user-images.githubusercontent.com/84182102/189152145-58b20a44-381a-406e-9feb-d5baf90d3e32.JPG)

For secured Kafka, it is necessary to create a new service StandardRestrictedSSLContextService with included Kafka certificate information. StandardRestrictedSSLContextService  is  property for SSL Context Service in the PublishKafka processor. A certificate for Kafka  should be provided in JKS format.

![sslservicee](https://user-images.githubusercontent.com/84182102/189151327-8e07fcbe-23e7-4352-b658-ea68e7f4c578.JPG)
