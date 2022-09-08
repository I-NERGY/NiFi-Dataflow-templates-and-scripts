## Read data in XLSX/CSV format, parse it into JSON and send  in Mongo and Kafka
In a  template is shown how to split and convert files from SFTP using python scripts. Python scripts are built for a specific use case and must be customized to suit your needs.
![xlsx_xls_csv](https://user-images.githubusercontent.com/84182102/189154319-3125e39f-7f60-424b-914b-50510d4d115a.JPG)

### How it works?
To receive files from SFTP  in NiFi Cluster environment it is necessary to correctly specify the properties for SFTP such as hostname, port, username, password, as well as the exact remote path to the directory from which the files are taken. Required properties should be entered both for ListSFTP and FetchSFTP processors. The sftp_gathering Process Group with mentioned processors is below.
![sftp_gathering](https://user-images.githubusercontent.com/84182102/189154756-0f903cbb-e1a2-40f0-a62f-bb303863e813.JPG)


In a NiFi cluster environment, it should be ensured that nodes do not receive the same files multiple times. This is achieved by setting "Primary node" for the Execution for ListSFTP processor in the Scheduling tab. 

RouteOnAttribute checks and sends files to specific python scripts depending on the file extension.
![image](https://user-images.githubusercontent.com/84182102/189155159-420efd91-8831-4067-b8a1-8b8d45703b84.png)

GetFile processor will take JSON files from directory at a certain period of  time and send them to Kafka and Mongo. Files after sending are deleted from the directory.

For secured Kafka, it is necessary to create a new service StandardRestrictedSSLContextService with included Kafka certificate information. StandardRestrictedSSLContextService  is  property for SSL Context Service in the Kafka processor. A certificate for Kafka  should be provided in JKS format.

 ![sslservicee](https://user-images.githubusercontent.com/84182102/189156033-0ef17951-e04c-4f54-ad26-77a346810813.JPG)
