## Simple SQL template
A DataFlow that queries an SQL database every day at midnight, collecting the data for the previous date.
![image](https://user-images.githubusercontent.com/90190347/189132848-668f3b80-370a-4fd5-b9d4-8cc3d027ac45.png)

## How does it work?
The DataFlow starts with a GenerateFlowFile processor that generates a flow file every day at 00:00, configured with CRON as on the picture below.
![image](https://user-images.githubusercontent.com/90190347/189133152-ffb26be6-27c5-4e29-a0ee-a9ffefc2fde1.png)
The next step is to somehow have the date for the previous date which will be used in a query. We do that by using the Update Attribute processor, allowing us to add custom attributes to the flowfiles. In this case we add the attribute named "date" and give it the value of "${now():toNumber():minus(86400000):format('yyyy-MM-dd')}" which returns us yesterdays date using [NiFi's expression Language](https://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html)
![image](https://user-images.githubusercontent.com/90190347/189134455-e07c5460-ddc8-429f-88e3-dbab77e44d3a.png)
Now we are ready to query the data, we will use ExecuteSQLRecord for that. The first thing required is a Connection Pooling Service for which we will create a simple DBCPConnectionPool service. 
![image](https://user-images.githubusercontent.com/90190347/189134928-848ed5cc-a742-4e67-8fea-5e2d8bef7e46.png)
DBCPConnectionPool configurations should look like on the image below
![image](https://user-images.githubusercontent.com/90190347/189135428-46b271c2-5549-4091-96a1-a2bdad9980aa.png)
Database Connection URL looking like: ```jdbc:sqlserver://HOST_HERE;encrypt=false;user=USERNAME;password=PASSWORD```
The instance should contain a proper Database Driver, in this case its SQLServerDriver with the classname ```com.microsoft.sqlserver.jdbc.SQLServerDriver``` and its path on ```/opt/nifi/nifi-current/conf/sqljdbc/ita/mssql-jdbc-10.2.0.jre8.jar```
Now that the connection is working correctly, we can finally query the data. In this case for "SQL select query" field we put ```SELECT * FROM table_name
WHERE date LIKE '${date}%';``` where we select all the data for the previous date. The flowfile attribute is accessed like in the example ```${date}```.

ExecuteSQLRecord processor requires one more service for writing results to a flowfile, a Record Writer. We will use a simple CSVRecordSetWriter with the default properties. 
![image](https://user-images.githubusercontent.com/90190347/189137402-50e5e806-6bac-413f-bd43-cec2b1d1103b.png)

