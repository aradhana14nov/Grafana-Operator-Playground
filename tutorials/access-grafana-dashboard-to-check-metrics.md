
### Access Grafana Dashboard and configure it to check your metrics


Step 1: 
Once the installation was a success, then Grafana should be available through your web browser  on http://##DNS.ip##:30200, and your screen should look like the image below. The username is root and the password is "secret".

Step2 :Configuring Your DataSource

Configuring your data source is the first step to setting up your Grafana dashboard. Your data source could be a database or a collection of logs. 
Now we’ll walk through configuring Grafana to connect to your MySQL database.

For my example, I’ve created a database named testdb with a table called population to simulate a database.

- The first option on the display is Create your datasource. 

Complete the web form with your connection details which will looks like below snapshot:

 I’m using a local MySQL database running on localhost on port 30685 for the database testdb.

User by which the above database is created : username :root, password : password

It’s safe to leave the rest of the fields as default. Hit save and test. 

If everything is configured correctly, you should see a green box with the message Database Connection OK.



### Create Your First Dashboard

Now database is connected, we can create a dashboard showing stats about the testdb database we are connected in the previous section.

Dashboards are made from panels of information organized into rows. 

Panels represent a visual representation of a query.Each panel can show the same or different data using a visualization that is the easiest for you to process. 

The first panel we are creating will show the total number of population in our testdb database.

### Single Stat Panel
To show a single number, we use the single stat panel.

On Grafana UI, you have the option create your first dashboard. 

Choose this option, then select Add Query.

Select your database from the Query drop-down menu and choose to format this query as a table in the format as drop-down. Select the Edit SQL link and paste the following SQL:

SELECT
  population
FROM Population
This assumes you have a table called Population and a column named population.

You can change the names of the column and the table to fit your data. Your Query page should look like the screenshot below:



Click the Visualization icon and choose Singlestat from the drop-down list options. This will immediately give you a preview of your panel.

Select Max as the value for Show, and you’ll see the maximum value of the population column. 

Add some color to the background and the number to personalize it. There is a huge amount of customization available to make it look exactly how you want.

Finally, click the settings icon to give the panel a meaningful name, such as Total Population. You have now created your first panel with a dashboard with a single panel something like the view below:




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

-  Click the graph title, then click "Edit".

- Under the "Metrics" tab, select your Prometheus data source (bottom right).

- Enter any Prometheus expression into the "Query" field, while using the "Metric" field to lookup metrics via autocompletion.

- To format the legend names of time series, use the "Legend format" input. For example, to show only the method and status labels of a returned query result, separated by a dash, you could use the legend format string {{method}} - {{status}}.

- Tune other graph settings using "Visualise" option until you have a working graph.

- The following shows an example Prometheus graph configuration:



### Conclusion 
You can display all your data (even from multiple sources) in whatever format works best for you. There is a wide selection of visualizations built in and accessible through the community. You can customize your panels with color and transparency—whatever makes sense for your visual. You can even make your own visualization plugins if you want something a little more specific to your use case.

