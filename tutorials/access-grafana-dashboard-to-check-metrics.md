
### Access Grafana Dashboard and configure it to show metrics

Logged in to the Grafana UI and configure datasource.

### Configure Your DataSource :

Data source could be a database(MySQL) or a collection of metrics(Prometheus). 

Here we will configure Grafana to connect MySQL database.

In earlier steps we have created a database named "testdb" with a table: "Population".

***Create your datasource as MySQL***

 Complete the web form with your connection details which will looks like below snapshot:
 
 database: testdb
 
 username: root
 
 password: password
 
 host : ##DNS.ip##:30685 

***Click on save and test***

 ![](_images/mysql-datasource-connection.PNG)

If everything is configured correctly, you should see a green box with the message Database Connection OK.



### Create Your First Dashboard

Now database is connected, we can create a dashboard showing stats about the testdb database we are connected in the previous section.

1. Click on New dashboard.

2. Click on "Dashboard settings". Give the dashboard Name and click on "Save".

3. Click on Add panel.Provide panel name.Give the panel a meaningful name, such as "mariadb-database-metric". 

4. From "Query" dropdown choose "MySQL".

5. Click on "Edit SQL".
   
   SELECT
     year as Year,population as Population
   FROM Population

   ![](_images/edit-sql.png)
   
   Add below query to fetch data from table "Population" from database "testdb":
   
   ![](_images/query-db-to-get-metrics.png)

6. Click on "Visualization" option to see metrics on different options like : Graph, Gauge, Bar Gauge etc.

7. According to the type of metrics we need to choose appropriate Visualization form. In this example we are using "Gauge" to see the database table data.

8. In calc option you can use the appropriate function to view the data.In below snapshot we are using "max" to check maximum population with the year details.

You have now created your first panel with a dashboard with a Gauge like below:


![](_images/mariadb-gauge-db-metrics-max-population.png)



### Creating a Prometheus data source

To create a Prometheus data source in Grafana:

- Click on the "cogwheel" in the sidebar to open the Configuration menu.

- Click on "Data Sources".

- Click on "Add data source".

- Select "Prometheus" as the type.

- Set the appropriate Prometheus server URL (for example, http://localhost:30100/)

- Click "Save & Test" to save the new data source.
  

###  Creating a Prometheus graph

Follow the standard way of adding a new Grafana graph :

-  Click the graph title(panel_list_name), then click "Edit".

   
   ![](_images/Dashboard-name-setting.png)
   
   
   ![](_images/panel_list_name.png)


- Under the "Metrics" tab, select your Prometheus data source (bottom right).


- Enter any Prometheus expression into the "Query" field, while using the "Metric" field to lookup metrics via autocompletion.
  

- Tune other graph settings using "Visualise" option until you have a working graph.


- The following shows an example Prometheus graph configuration:

![](_images/metric-for-global-status-commands-total.png)


### Conclusion 
You can display all your data (even from multiple sources) in whatever format works best for you. There is a wide selection of visualizations built in and accessible through the community. You can customize your panels with color and transparency—whatever makes sense for your visual. You can even make your own visualization plugins if you want something a little more specific to your use case.

