## Read data in XML format, parse it into JSON, and send  in Mongo and Kafka
A template shows how to get XML files from the SFTP server and prepare them for storage in JSON format.
![image](https://user-images.githubusercontent.com/84182102/189157668-816f35a3-a429-41d8-ad08-e0e278e61250.png)


## How it works?
To receive XML files from SFTP  in the NiFi Cluster environment it is necessary to correctly specify the properties for SFTP such as hostname, port, username, password, as well as the exact remote path to the directory from which the files are taken. Required properties should be entered both for ListSFTP and FetchSFTP processors. The sftp_gathering Process Group with mentioned processors is below.
![sftp_gathering](https://user-images.githubusercontent.com/84182102/189157897-9f93d929-b4f6-438e-9808-d1af36ed4aad.JPG)


In a NiFi cluster environment, it should be ensured that nodes do not receive the same files multiple times. This is achieved by setting "Primary node" for the Execution for ListSFTP processor in the Scheduling tab. 

For conversion  XML file use  TransformXML processor and specify xmltojson.xslt or xml-to-json.xsl stylesheet  as "XSLT file name". It converts any XML document to JSON format.

![transform](https://user-images.githubusercontent.com/84182102/189159159-cab3a3b5-64a4-4461-b7ec-78c95688375b.JPG)


For secured Kafka, it is necessary to create a new service StandardRestrictedSSLContextService with included Kafka certificate information. StandardRestrictedSSLContextService  is  property for SSL Context Service in the Kafka processor. A certificate for Kafka  should be provided in JKS format.

![sslservicee](https://user-images.githubusercontent.com/84182102/189157969-b5518640-282b-4498-a9c4-f64e8680e77c.JPG)
