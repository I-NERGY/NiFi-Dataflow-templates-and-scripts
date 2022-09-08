## Read data in XLSX/XLS/CSV format, parse it into JSON and send  in Mongo and Kafka
In a  template is shown how to split and convert files using python scripts. Python scripts are built for a specific use case and must be customized to suit your needs.
### Image
### How it works?
To receive files from SFTP  in NIFI Cluster environment it is necessary to correctly specify the properties for SFTP such as hostname, port, username, password, as well as the exact remote path to the directory from which the files are taken. Required properties should be entered both for ListSFTP and FetchSFTP processors
### Image

In a NIFI cluster environment, it should be ensured that nodes do not receive the same files multiple times. This is achieved by setting "Primary node" for the Execution for ListSFTP processor in the Scheduling tab. 

RouteOnAttribute checks and sends files to specific python scripts depending on the file extension.

GetFile processor will take JSON files from directory at a certain period of  time and send them to Kafka and Mongo. 

For secured Kafka, it is necessary to create a new service StandardRestrictedSSLContextService with included Kafka certificate information. StandardRestrictedSSLContextService  is  property for SSL Context Service in the Kafka processor. A certificate for Kafka  should be provided in JKS format.
### Image 