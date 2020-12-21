
### Access Grafana Dashboard and configure it to check your metrics


- Logged in to the Grafana UI 

- Configure Your DataSource

 Data source could be a database or a collection of logs. 
 Here we will configure Grafana to connect MySQL database.

In earlier steps we have created a database named testdb with a table: Population.

- Create your datasource as MySQL

 Complete the web form with your connection details which will looks like below snapshot:
 
 database: testdb
 
 username: root
 
 password: password
 
 host : ##DNS.ip##:30685 

- Hit save and test. 

If everything is configured correctly, you should see a green box with the message Database Connection OK.



### Create Your First Dashboard

Now database is connected, we can create a dashboard showing stats about the testdb database we are connected in the previous section.

Dashboards are made from panels of information organized into rows. 

Panels represent a visual representation of a query.  

The first panel we are creating will show the total number of population in our testdb database.

### Single Stat Panel

To show a single number, we use the single stat panel.

- On Grafana UI, you have the option create your first dashboard. 

  Choose this option, then select Add Query.

- Select your database from the Query drop-down menu and choose to format this query as a table in the format as drop-down. 

- Select the Edit SQL link and paste the following SQL:

SELECT
  population
FROM Population


 This assumes you have a table called Population and a column named population.

- Your Query page should look like the screenshot below:



- Click the Visualization icon and choose Singlestat from the drop-down list options. This will immediately give you a preview of your panel.


- Select Max as the value for Show, and you’ll see the maximum value of the population column. 


- Add some color to the background and the number to personalize it. There is a huge amount of customization available to make it look exactly how you want.


- Finally, click the settings icon to give the panel a meaningful name, such as Total Population. 


You have now created your first panel with a dashboard with a single panel something like the view below:




### Creating a Prometheus data source

To create a Prometheus data source in Grafana:

- Click on the "cogwheel" in the sidebar to open the Configuration menu.

- Click on "Data Sources".

- Click on "Add data source".

- Select "Prometheus" as the type.

- Set the appropriate Prometheus server URL (for example, http://localhost:30100/)

-  Adjust other data source settings as desired (for example, choosing the right Access method).

- Click "Save & Test" to save the new data source.
  
The following shows an example data source configuration:




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

