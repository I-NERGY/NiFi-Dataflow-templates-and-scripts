## Read data in CSV format, parse it into JSON and send  in Mongo and Kafka
The template shows how to get CSV files from the SFTP server and prepare them for storage in JSON format. 
### Image

## How it works?
To receive files from SFTP  in the NIFI Cluster environment it is necessary to correctly specify the properties for SFTP such as hostname, port, username, password, as well as the exact remote path to the directory from which the files are taken. Required properties should be entered both for ListSFTP and FetchSFTP processors.

 ### Image
 
In a NIFI cluster environment, it should be ensured that nodes do not receive the same files multiple times. This is achieved by setting "Primary node" for the Execution for ListSFTP processor in the Scheduling tab. 

After receiving CSV files from SFTP, files are further sent to the SplitText processor to be split if they are larger than the defined property. 

With ConvertRecord, the split files are converted to JSON and stored in Mongo and Kafka. The comma operator(,) is a default separator. If there is a need to change that option it is necessary to customize the CSVReader service for that processor.

For secured Kafka, it is necessary to create a new service StandardRestrictedSSLContextService with included Kafka certificate information. StandardRestrictedSSLContextService  is  property for SSL Context Service in the Kafka processor. A certificate for Kafka  should be provided in JKS format.
### Image