# NiFi Dataflow templates and scripts
Apache NiFi is a tool that is built to automate the flow of data between systems. NiFi’s fundamental design concepts closely relate to the main ideas of Flow Based Programming, resulting in data flows being directional graphs. Data is represented within FlowFiles which are objects moving through the system, each containing attributes in a form of key/value pairs and its associated content of zero or more bytes. 
Parts of a NiFi Data Flow:
+ Each node in the graph is a FlowFile Processor which perform actual work. FlowFile Processors are doing a combination of data routing, transformation or mediation between systems. They have access to attributes of a given FlowFile and its content stream.
+ Eeach edge in the graph is a Connection/Queue. Connections provide an actual linkage between processors in a form of a queue that allow various processes to interact at differing rates. 
+ The Flow Controller maintains the knowledge of how processes connect and manages the threads and allocations thereof which all processes use. The Flow Controller acts as the broker facilitating the exchange of FlowFiles between processors.
+ A Process Group is a specific set of processes and their connections, which can receive data via input ports and send data out via output ports. In this manner, process groups allow creation of entirely new components simply by composition of other components.

For custom script execution there are multiple processors to choose from, but in these examples we use ```ExecuteStreamCommand``` which can execute an external command on the system that NiFi is running and make an output FlowFile with the results of the command. In this case we use it to execute Python scripts, but any language can be se used in the similar manner.
NiFi templates containing custom data flows and python scripts
### Asset type
Library
### Technical Categories
Data Processing
### Business Categories
Energy
### Search Keywords
data, dataflow, nifi, template, python, ETL, data_processing
### Developed by
[Engineering Ingegneria Informatica S.P.A](https://www.ai4europe.eu/ai-community/organizations/company/engineering-ingegneria-informatica-spa)
### Detailed Description
Apache NiFi offers a concept of Templates which let us share and reuse the already made dataflows.
Steps for uploading and importing a template:

  1.  Right click on empty space in NiFi and select Uplad Template
  
  ![image](https://user-images.githubusercontent.com/90190347/191253287-ebdf737e-01f6-4fba-ac7b-1d3b9865584f.png)

  2. Select the template on a local machine
  3. Import a template from the toolbar

  ![image](https://user-images.githubusercontent.com/90190347/191253598-7daf98dc-1fdb-4377-ae42-3e6f0a1f4e8f.png)


The asset contains multiple NiFi templates in XML format that are ready to be imported, covering examples for SQL queries, MQTT and SFTP clients, API usage, flowfile attribute manipulation and more. Assets also come with additional python scripts that are used in appropriate dataflows. 
### Related Projects
[I-NERGY](https://www.ai4europe.eu/ai-community/projects/i-nergy)
### License
n/a
