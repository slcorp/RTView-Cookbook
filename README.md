# RTView-Cookbook

Welcome to the RTView Cookbook.

This cookbook contains a number of "recipes" (referred to herein as "Patterns") that show solutions to the most commonly required tasks using RTView.

The intention of the cookbook is to provide a reference to these tasks while clearly showing how they can be accomplished by an end user of any experience level. Completed demos are provided for the cookbook, but we suggest that the recipes are followed as they contain a great deal of "best practice" information and clarify steps that are sometimes subtle enough to be overlooked that are important to a properly running visualization.

# System Requirements
* RTView Core 6.x
* Java 1.5+

# Useful Links
## Download RTView: http://sl.com/evaluation-request/
## Download Java: http://www.oracle.com/technetwork/java/javase/downloads/index.html

# Using The Cookbook

Please make certain that the environment variable RTV_USERPATH inlcudes the simulated data used by the cookbook, mysimdata.jar.

This is done by adding "mysimdata.jar" to RTV_USERPATH as a global environment variable, or by manually setting RTV_USERPATH from the command line each time the cookbook is used. Mysimdata.jar has been included in each pattern, but if you wish to add it to the lib path then it sohuld be added as a relative path explicitly (e.g. c:\Program Files\RTV_xxx\lib\mysimdata.jar").

# Running the Examples
1. Install and configure RTView Builder in your local environment. 
2. Include mysimdata.jar in the RTV_USERPATH as described above. 
3. Bring up a command window and initialize it (e.g. rtv_init.bat). 
4. In the command window, type run.bat to run the Builder with the simulator. The simulator supplies the necessary simulated data for the displays. 
5. Open up the displays listed below to see it working in the Builder preview window. 

# Patterns

##Pattern 1 - Create and Display a Current Value Table

*Goal*	

A common requirement for many is the need to display current values from a data source asynchronously by creating a cache of the data. This is sometimes referred to as a current value table.
 	 
*Challenge*	

We are receiving asynchronous data - data that comes in at different times. If a cache is not used, then this data would be seen as it comes in - one line at a time. By using the RTView caching solution to cache the data we are able to visualize all of the lines in tabular form, each with a timestamp,
 	 
*How To	Capture and Cache Data*

First create the cache and populate it with asynchronous data. This is done by using "Tools->Caches..." and clicking on "Add". Name the cache something pertinent, in this case ServerComponentStats will do, and click on "OK". Please see the prelimary notes, step 3 under RTView Display Builder, for more information on this.

2. With the cache selected in the "Caches" dialog we opened above, attach data to the cache by going to "Object Properties->value Table" and right clicking. Make certain the data source is recognized by RTView Display Builder as described in the preliminary notes (item 2 under RTView Display Builder). This will bring up a menu from which you want to select Attach to Data.

3. Pick your data type and select it from the drop down menu in the dialog that appears. We are using a data simulator for this pattern (MySimDs) and the data it produces is what the cache will be connected to. MySimDs simulates data that is typical of a server monitoring environment.

4. The Data Key in this example should be ServerComponentStats, and all (*) Columns should be selected. Select OK to apply the changes and close the dialog.

5. In the Object Properties for the cache, indexColumnNames must be set to "Server;Component" This specifies which columns from the incoming data are to be used as indexes. This is a central concept to the creation of a Current Value Table. Please refer to the Preliminary Notes, Concepts of Note, Cache Information for a description of the purpose of indexing.

6. After attaching data to the cache save your cache definition to disk in an .rtv file. Please refer to the Best Practices document for naming conventions, in this example we name our cache definition file "server_cache.rtv".

7. Create a new document - save the table into a different .rtv file than the cache. Activate the cache in RTView Dislpay Builder which results in it being loaded into memory. This is done via "Tools->Options->Caches->Add" and selecting the .rtv file you saved to disk and then selecting "Save". This step is described further in the Preliminary notes under "RTView Display Builder" item 4.

8. In RTView Display Builder - using the Edit menu go to "Edit->Add..." to add a table. In this case, add the table labeled "Table". Add by clicking in the main window at the location you want your table added.

9. Select the table, and in its Object Properties under "Data->valueTable" right click to get the Attach to Data submenu, and select Cache. In the resulting dialog, select the cache you created in Step 1 of this walk through in the Cache drop down menu (if you do not see your cache listed here, then it was not successfully added to RTView Display Builder as listed in step 6 above). In this dialog, for Table choose "current" and for Column(s) choose * (for all columns). Then hit "OK" to apply these changes.

The table you created should now display the data from your previously created cache. Save your current value table to disk if you wish.

##Pattern 2 - Show a Subset of Incoming Data in a Table
*Goal*	

A common requirement when graphically visualizing large amounts of data is filtering. In this pattern we expand on Pattern 1 by allowing users to filter a subset of the data coming in from a cache connected to a data source.
 	 
*Challenge*	

Drop down menus will filter the type of data we want to see in the table. These drop down menus should be auto populated by the data coming in so that we don't need to manually reenter the data.The second drop down menu is affected by the first;. This is because each of the servers have a different number (and type) of components. When we pick a server from the menu, we only want to see the components in the second drop down menu that apply to the server we have picked in the first drop down menu. This is done using Variables and Functions within RTView Display Builder which are discussed here and in the preliminary notes.

We may want to see all of the components of a given selected server, or all of the servers and all of the components, so we need to add an option to see all servers or to see all components to the menus. We also need to make certain that the component menu does not have invalid values at any point.

*How To	Add Objects*

1. Create the cache as in Pattern 1 and make certain the cache is connected to your data source (Pattern 1, steps 1-6).
2. Add the table we will be viewing and give it a label (Object Properties, Label->Label), such as "Servers Table".
3. Add a drop down menu for Servers - this will allow us to select a specific server. A drop down menu control object can be retrieved by going to "Edit->Add..." and then to the "Controls" tab.
4. Add a drop down menu for Components. This menu will be automatically modified by whatever is selected from the Server menu to reflect the components available on a selected server by the functions we will add below.

*Create and Add Variables*

5. Add a variable for servers by going to Tools->Variables. Type in a variable name, in this case we use $server. For the initial default value, use * for "All Servers". Then click on Add.

6. Add a variable for components as well, called in this case $components. As with $server above, set an initial default value of * for All Components and then click on add to add this variable.

*Create and Add Functions*

In Tools->Functions, we must now add the functions that will allow us to filter the Components menu based on the selection in the Servers menu. Please refer to the section called "Functions" in the Preliminary Notes for more information on functions and their use.

7. dataCurrentForServerComponent - The first function is a pointer to the cache. Call this (in this examples case) dataCurrentForServerComponent. It's Function Type is "Reference". Right click on Table, Attach to Data->Cache and select the cache you created in step 1. Also set Filter Rows to "Basic", Filter Column to: "Server;Component" and Filter Value to "$server;$component". This reference function is what is used to populate our table object later, as discussed in item 13 below.

8. serverListAll - Add a function that will be utilized to populate the list of servers in the Servers drop down menu. Call this function serverListAll and give it a Function Type of "Create Selector List". Attach the cache to this function (Table->Attach to Data->Cache) and use "Server" for Column(s). Sort Values for serverListAll should be set to 1 and Sort Descending should be set to 0.

9. componentListAllForServer - Add a function that will be utilized to populate the list of components in the Components drop down menu. Call this function componentListAllForServer and give it a Function Type of "Create Selector List". We are going to attach this function to the cache data and use "Component" in Column(s). We also need to set Filter Rows to Basic, Filter Column to "Server" and Filter Value to the variable $server. After selecting OK we set Sort Values to 1 and Sort Descending should be set to 0.

10. validateComponent - It is important to validate the values in the Component menu. When a new server is selected, the Component menu might contain data that is out of sync with the currently selected Server. For this use a function called "validateComponent" and give it a type of "Validate Substitution". The Substitution String should be set to the $component variable, and this function should be attached to the componentListAllForServer function and given Column(s) of Selector.

Please note that the function type "Validate Substitution" can also be used to set default values. Because we are able to set all (*) on both our server menu and component menu in this pattern, we do not use it this way. However, it is used in later patterns for the purpose of setting default values.

Here is a chart of the relationship function relationships to objects, data and each other:

*Attach Data to Objects*

11. Attach our filtered data to our objects. Starting with the Servers drop down menu, using it's Object Properties we set Data->ListValues to the function we created in step 9 called serverListAll (which gives us all the servers), and set selectedValue to our variable $server as well as setting varToSet to $server.

12. Set the Component drop down menus Object Properties. Set Data->ListValues to the function componentListAll, and we set selectedValue to our variable $component as well as setting varToSet to $component.

13. Attach data to our table using it's Object properties. Under Data->valueTable, attach to the function we created called dataCurrentServerComponent and use All (*) Columns. Please note that we attach this table to a Reference function rather than directly to the cache because the use of a reference function in later patterns becomes important. Using a reference function this way allows for a convenient and modular location to filter data.

##Pattern 3 - Trend Current and Historical Data
*Goal*	

We want to see a trend chart that reflects the historical data coming in on a specific component of one server and regularly updates to display current data coming in.
 	 
*Challenge*	

Show the history of only one component on one server at a time, and provide two drop down menus to choose which server and component history will be displayed. In this example, seeing all servers or components is not an option.
Optimise the data so that the initial data is loaded only once rather than reloaded over and over. We then only need to load new data coming in to update the trend chart.

Set an initial default for the server and component values.

How To	Several characteristics of this pattern are similar to Pattern 2. For example,

1. Create the cache as in pattern 1 and make certain the cache is connected to your data source. Make certain that the cache has a history for this pattern as well. This is done by adding a value to the cache property of "maxNumberOfHistoryRows". For this pattern we set it to an arbitrarily high value of 10000.

2. Add the trend graph we will be viewing and give it a label (Object Properties, Label->Label), such as "Server Component Table". For this trend graph set traceCounts to 1 (as we are only looking at one trend) and the Time Range and Time Shift properties to -1.

3. Add a drop down menu for Servers - this allows for the selection of a specific server.

4. Add a drop down menu for Components. This menu will be modified by whatever is selected from the Server menu to reflect the components available on a selected server by the functions we will add below.

*Create and Add Variables*

5. Add a variable for servers by going to Tools->Variables. Type in a variable name, in this case we use $server.

6. Add a variable for components as well, called in this case $components.

*Create and Add Functions*

In Tools->Functions, add the functions that will allow us to filter the Components menu based on the selection in the Servers menu.

7. dataHistoryForServerComponent - The first function is a pointer to the cache. Call this (in this examples case) dataHistoryForServerComponent. It's Function Type is "Reference". Right click on Table, Attach to Data->Cache and select the cache you created in step 1. Because we are looking at the historical data, and not the current data, set Table to "history". We will set Column(s) to "time_stamp;CpuUsage;". In order to optimise the data that comes in so that the entire history is not sent with every update but only the most recent history is, check the "Update Once" button. Set Filter Rows to "Basic", Filter Column to "Server;Component" and Filter Value to "$server;$component".

8.dataCurrentForServerComponent - Create another pointer to the cache that is very similar to the last function, except that it will point to the current data coming in rather than the history. Because of this the previous function (dataHistoryForServerComponent) can simply be copied (the new function will be named dataCurrentForServerComponent) and "History" can be changed to "Current". As with the last function, the Function Type is "Reference". Right click on Table, Attach to Data->Cache and select the cache you created in step 1.

Because we are looking at the historical data, and not the current data, set Table to "history". We will set Column(s) to "time_stamp;CpuUsage;". In order to optimise the data that comes in so that the entire history is not sent with every update but only the most recent history is, check the "Update Once" button. We will also set Filter Rows to "Basic", Filter Column to "Server;Component" and Filter Value to "$server;$component".

9. serverList - Add a function that will be utilized to populate the list of servers in the Servers drop down menu. Call this function serverList and give it a Function Type of "Create Selector List". We are going to attach the cache to this function (Table->Attach to Data->Cache) and use "Server" for Column(s). After clicking on OK, Sort Values for serverList should be set to 1 and Sort Descending should be set to 0. 

10. componentListForServer - Add a function that will be utilized to populate the list of components in the Components drop down menu. Call this function componentListForServer and give it a Function Type of "Create Selector List". Attach this function to the cache data and use "Component" in Column(s). Set Filter Rows to Basic, Filter Column to "Server" and Filter Value to the variable $server. After selecting OK set Sort Values to 1 and Sort Descending should be set to 0.

11. validateServer - In order to make certain we set a proper initial default value in the server menu use a function called "validateServer" and give it a type of "Validate Substitution". The Substitution String should be set to the $server variable, and this function should be attached to the componentListAllForServer function and given Column(s) of Selector.

12. validateComponent - It is important to validate the values in the Component menu. When a new server is selected, the Component menu might contain data that is out of sync with the currently selected Server. Use a function called "validateComponent" and give it a type of "Validate Substitution". The Substitution String should be set to the $component variable, and this function should be attached to the componentListAllForServer function and given Column(s) of Selector.

*Attach Data to Objects*

13. Attach our filtered data to our objects. Starting with the Servers drop down menu, using it's Object Properties we set Data->ListValues to the function we created in step 8 called serverList (which gives us all the servers), and we set selectedValue to our variable $server as well as setting varToSet to $server.

14. Set the Component drop down menus Object Properties. Set Data->ListValues to the function componentList, and we set selectedValue to our variable $component as well as setting varToSet to $component.

15. Attach data to our trend graph using it's Object properties. Under Trace 01->trace1Value attach to data->function to dataCurrentForServerComponent and use time_stamp and Component for Column(s) (in that order). Under Trace 01->trace1ValueTable, attach to the function dataHistoryForServerComponent. Use "time_stamp" and "CpuUsage" for columns, in that order.

##Pattern 4 - Show Multiple Trends of Synchronous Data

*Goal*

A common need is to create an RTView object that can be reused and individualized with the data it displays. The Composite object will do this. By setting variables and functions that are exposed when the Composite object is instanced, its behavior reflects the exposed variable values.

*Challenge*

Show how to create and instance a composite object using a previously defined trend graph  as well as a  other newly added graphic object(s),  Connect this  instance of the composite object  to  a drop down menu that sets one server at a time.  The Composite object will reflect the data from the server selected from the drop down menu.

*How to*

Create a composite object  as a separate .rtv file named myComposite.rtv. This composite object will later be instanced into pattern4.rtv

From the RTView Builder add a rectangle (obj_rect) to the center of the screen. The size of the rectangle can be set with these object properties: objHeight, objWidth. Suggested sizes are objHeight: 138 , objWidtth 317.
Add a obj_vuscale inside the left side of the obj_rect.  Add an obj_trendgraph02 to the right of the obj_vuscale. Size each object to fit within the rectangle.

Now change the size of the background to just be slightly larger than the rectangle and objects within the rectangle.  From the Builder , choose  File and then Background Properties.  In the “Background Properties” dialog set Model Width: 318 and Model Height: 140.  Hit OK and move all the objects to fit exactly within the new background rectangle.

*Create and Add Variables*

4. Add a variable for servers by going to Tools->Variables. Type in a variable name  $server. This will be a substitution variable and the “Use as Substitution” box should be automatically checked. Now add another variable “server”  which is a local variable. Be sure that the “Use as Substitution” is unchecked.

5. Add a variable for the trend graph’s timeRange object property by going to Tools->Variables. Type in a variable name  $timeRange which is a substitution variable and the “Use as Substitution” box should be automatically checked.  Now add another variable by typing in the name timeRange which is a local variable. Be sure that the “Use as Substitution” is unchecked.

*Create and Add Functions*

In Tools->Functions, add the functions that will allow us to filter the Components menu based on the selection in the Servers menu. 

6. dataHistoryForServer - The first function is a pointer to the cache. Call this (in this examples case) dataHistoryForServer. It's Function Type is "Reference". Right click on Table, Attach to Data->Cache and select the cache you created in pattern3, ServerComponentStats. Because we are looking at the historical data, and not the current data, set Table to "history". We will set Column(s) to "time_stamp;Server;LiveCount;CpuUsage;". In order to optimize the data that comes in so that the entire history is not sent with every update but only the most recent history is, check the "Update Once" button. Set Filter Rows to "Basic", Filter Column to "Server" and Filter Value to "$server".

7.dataCurrentForServer - Create another pointer to the cache that is very similar to the last function, except that it will point to the current data coming in rather than the history. Because of this the previous function (dataHistoryForServer) can simply be copied (the new function will be named dataCurrentForServer) and "History" can be changed to "Current". As with the last function, the Function Type is "Reference". Right click on Table, Attach to Data->Cache and select the cache you created in step 1. Because we are looking at the current data, and not the history data, set Table to "current". We will set Column(s) to " time_stamp;Server;LiveCount;CpuUsage;". We will also set Filter Rows to "Basic", Filter Column to "Server" and Filter Value to "$server". Leave the “Update Once” button unchecked.

8. dataTotalsByServerHistory - This Function Type is "Group By Unique Values". Right click on Table, Attach to Data->Function and select the Function Name dataHistoryForServer and Columns  *. This is the function created in step 6. Type in these values for the remaining arguments to the function:
  
The Group By Unique Values function will use the columns time_stamp and Server to be indices. A unique combination of time_stamp and server will create a new row in the returned table, such that the remaining columns will have their values added.  So for example given this initial data, there are 4 rows that have the same time_stamp and Server. 
 
The function will output a new table and place a single row for that time_stamp (Aug 31,2016 etc)  and Server (SERVER-10) , but add the values of LiveCount to be (6+11+6+5) or 28.

9. dataTotalsByServerCurrent - The previous function (dataTotalsByServerHistory) can simply be copied (the new function will be named dataTotalsByServerCurrent) and "History" can be changed to "Current". As with the last function, the Function Type is "Group By Unique Values". and Columns  *. Right click on Table, Attach to Data->Function and select the Function Name dataCurrentForServer and Columns  *. This is the function created in step 7. Type in the same values for the remaining arguments to the function as in step 8.

10. setServer  - This Function Type is “Set Substitution”. For the argument “Substitution String”, type in $server.  For the argument “value”, press the right mouse button and choose Attach to Data-> Variable.  From the “Attach To Variable Data”  dialog, choose “server” from the Variable Name  list box.

11. setTimeRange  - This Function Type is “Set Substitution”. For the argument “Substitution String”, type in $timeRange.  For the argument “value”, press the right mouse button and choose Attach to Data-> Variable.  From the “Attach To Variable Data”  dialog, choose “timeRange” from the Variable Name  list box. 

*Attach Data to Objects*

12. Starting with the obj_vuscale, using it's Object Properties we set Data->values to the function we created in step 9 called dataTotalsByServerCurrent (which gives us the current data totals for a given server), Restrict the Column(s) to CpuUsage. Since the variable $server is not set at this point, no data will be displayed in the obj_vuscale. This is also the purpose of a composite object, that it is very general.  Once this object is instanced, it can be set to a specific server. 

13. Attach data to our trend graph using it's Object Properties. Under Trace 01->trace1Value Attach to Data->FUNCTION to dataTotalsByServerCurrent and use time_stamp and LiveCount for Column(s) (in that order). Under Trace 01->trace1ValueTable, attach to the function dataTotalsByServerHistory. Use "time_stamp" and "LiveCount" for columns, in that order. The data attachments for the trend graph are similar to that done in pattern 3. In both cases trace1Value  attaches to current data and trace1ValueTable attaches to history data.

14. Save your work  to the file myComposite.rtv.

*Instance the Composite Object*

15.  Open a new file pattern4.rtv.  From the Builder  choose File->Add to bring up the Object Palette.  From the Composite Tab, choose a composite object and bring it into the work area.

16. From the Object Properties of that composite object, set Composite->rtvName  to myComposite by choosing that name from the list box. This is what should appear on the display
 
17. Add to Tools->Variables the variable $server.  Add these functions to Tools->Functions componentListAllForServer, dataCurrentForServerComponent, serverList. validateComponents. These have been previously defined and can be copied from pattern2 and pattern3.

18. Add a list box, obj_c1combobox,  and set these Object Properties Data->:listValues is attached to the function serverLIst, Data->selectedValue and Data-> varToSet are attached to the variable $server.

19. Finish setting the Object Properties for Composite->server is set to $server by Attach to Data -> Variables and choosing $server from the list box.  Set  Composite->timeRange is set to 60.
 
Show a trend chart that reflects the historical data coming in for one selected server  and then updates the trend chart to display current incoming data. Show a vu-scale that shows the CPU usage for one server’s current data. For example, if SERVER-10 is selected from the list box, the CPU and trend chart will reflect data from that server.

*Additions*
More than one instance of  the Composite object, myComposite can be placed onto the screen and controlled by setting the Object Properrty  Composite->server to one of the servers, SERVER-1 thru SERVER-10. For example  by adding 2 more instances of myComposite, and setting one to SERVER-8 and the second to SERVER-9 the data displayed is unique to each server. So instead of allowing  a list box selection to set the server, it instead is hard-coded into the Object Property Composite->server. In the below example the  timeRange Object Property is set to 360 (minutes).

##Pattern 5 - Show Multiple Trends of Asynchronous Data

*Goal*	

A common need is to display a table made up of individual Composite displays.  Each row in the table will be a single Composite display.  Each Composite display can  access and display the data in that row.  The entire table will be a grid of Composite displays.
 	 
*Challenge*	

Show how to create the Composite Object Grid by using the Composite displays from pattern4. Furthermore, have the Composite Object Grid reflect  tablular data.

*How To*	

Several characteristics of this pattern are similar to pattern 4.  This pattern uses the Composite display, myComposite.rtv, built  in pattern4.

1. In the Builder add a composite object grid. Choose File-> Add.  From the Object Palette choose the Tables tab and pick the “Object Grid”.   Place an instance of the “Object Grid” into the Builder’s workspace.

*Add Functions*

2. Set the Object Properties  Data->valueTable  to the function “serverList” and choose the Column “Selector”. This function is the same as that  in pattern4.  Just copy it to this pattern. 
Note: To copy a function, open the Builder and File->Open pattern4.  Now go to Tools->Functions and click on the serverList function and  Edit->Copy.  Now File->Open pattern5 and Edit->Paste.   The function serverList should be added to the list of functions. 

*Expose Variables and Attach Data to Objects*

3. Set the Object Properties  Icon->iconProperties by double clicking on  “iconProperties:”  to bring up the “Icon Properties” dialog.

4.  At the very top of the “Icon Properties” dialog, set the “Icon Class Name”  to obj_composite from the list box.  Then, under the “Property Name” column find “rtvName”. Now  go across to the “Property Value” column and set this to “myComposite.rtv” from the list box provided.

5. When the myComposite.rtv was created in pattern4 ,  two variables were added and are exposed here:  “timeRange”  and “server”.   Set “timeRange” to 60.  Find the variable  “server” in the “Property Name” column. The next column across from this is the “Map” column.  Click on the string “Default” and choose “Column” from the list box. The next column across is “Property Value”. Choose “Selector” from the list box.

*Result*	

A grid of  Composite dispays trending “Live Count” data for each of the 11 servers is created. Now recall that in pattern4  the trend graph was attached to cache current and historical data that was filtered by  $server. We have now set 11 servers from the serverList function. Each Composite display in the grid  behaves independently.

*Additions*	

By setting the timeRange value from 60 (minutes) to a larger or smaller number, will display a greater or smaller range  over time.

##Pattern 6 - Show Bar Graph to Compare values   
Bar graph filtered by list box
##Pattern 7 - Show Bar Graph for Group Total Values   
Bar graph group by sum of total values
##Pattern 8 - Show Bar Graph for Group by Average Total Values   
Bar graph group by average of total values
##Pattern 9 - Heatmap

*Goal*	

There is a need for a graphics object  using a grid of colored rectangles to visually show numerical values as a color gradient. The heatmap is a grid of colored rectangles that can display up to two numeric values.  The rectangle color,  maps one value and the rectangle size,  maps a second value.

*Challenge*	

Show how to create  a heatmap and attach it to the columns “CpuUsage” and “Memory Usage” for the index columns, “Server” and  “Component”.   Also add a slider that sets the maximum CpuUsage that can be displayed  on the heatmap. 

*How To*	

A Composite object is used in this pattern.  Composite objects are introduced in  Pattern 4. 

1. Add a heatmap. In the Builder bring up the Object Palette dialog by choosing Edit->Add.  Choose the Graphs tab and bring a heatmap into the Builder’s workspace.

2. Add a slider to the display above the heatmap. From the Object Palette choose the Controls tab and bring a obj_c1scale into the Builder’s workspace. The slider will set the value of CpuUsage.

3. Again from the Object Palette Controls tab, bring in an obj_c1button into the Builders workspace and place it in the upper right side of the display.  Pressing the button will create a new window containing all the graphics created.

4. Add a composite object  to create the color legend that works in conjunction with the slider.  

*Create and Add Variables*

5. Add the  variable  $valueMax.  Provide an initial value to $valueMax of  “15.0”. $valueMax is set as the slider is moved from its min value to its max value. 

*Create and Add Functions*

In Tools->Functions, add the functions to sort the ServerComponentStats cache and restrict the number of columns to only two.

6. dataCurrentForServerComponentCpuUsage - The first function is a pointer to the cache. Call this (in this examples case) dataCurrentForServerComponentCpuUsage. It's Function Type is "Reference". Right click on Table, Attach to Data->Cache and select the cache you created in pattern3, ServerComponentStats. Because we are looking at the current data, set Table to "current". We will set Column(s) to "Server;Component;CpuUsage;UsedMemory;".  

7.  sortDataCurrentForServerComponentCpuUsage - This Function Type is "Sort Table".  Right click on Table, Attach to Data->Function and select the Function Name  dataCurrentForServerComponentCpuUsage  and Columns  *. This is the function created in step 6. 

*Attach Data to Objects*

8. The heatmap’s Object Properties-> Data valueTable is set to the Function dataCurrentForServerComponentCpuUsage.   There are 2 more Object Properties in the Data section of note: nodeIndexColumnNames and sizeValueGroupType.   From our standard documentation, this explanation of how it works. “ Tabular data attached to the valueTable property must contain one or more index columns and at least one data column. The heat map will display one level of nodes for each index column specified. Use the nodeIndexColumnNames property to specify column names. The first non-index numeric data column is used to control the size of the node. The second non-index numeric data column is used to control the color of the node.”

The nodeIndexColumnNames are set to “Server” and “Component”. The data coming in from the function dataCurrentForServerComponentCpuUsage contains 4 columns, the first two are index columns Server and Component, the last two are numeric columns UsedMemory and CpuUsage.  The numeric values of  UsedMemory will control the size of the rectangle node and the numeric value of CpuUsage will control the color of the node. The sizeValueGroupType is set to “sum”. 

Note: Usually with using  group by functionality, several rows of data must be aggregated as either “sum”, “average”, “min” or “max”. But in this case the data using the indices “Server” and “Component”  comes out as a single row for each combination of the 2 indices. No grouping is done or needed.

9. The slider (obj_c1scale) needs to have these Object Properties set. Data->value and Data->varToSet  need to Attach to Data-> Variable  $valueMax.  Set the minimum and maximum values of the slider  Data->valueMax : 100.0  and Data->valueMin: 0.0.  By moving the slider the  numeric value set to the variable $valueMax will range from 0.0 to 100.0.

10. The heatmap needs to set some more Object Properties. These properties set the range of colors.  Data->ColorValueMin 0.0 and Data->ColorValueMax is set to the variable $valueMax.  Data Format-> maxColor and Data Format-> minColor are set with a color chooser by clicking on “…” at the very right side of the row.
 
11.  The obj_c1button in the top right hand corner of the display  will let the user drilldown to a new window containg the heatmap and slider.   The Object Property  Interaction->actionCommand  will bring up the “Define System Command” dialog. Choose “Drill Down or Set Substitution”, then choose “Edit Drilldown Target”.                                                                                          
*Instance the Composite Object*
12.  The composite object to be  added to the  display is pre-built. From the Builder  choose File->Add to bring up the Object Palette.  From the Composite Tab, choose a composite object and bring it into the work area.  From the Object Properties of that composite object, set Composite->rtvName  to sub_hmlegend  by choosing that name from the list box. Set the Object Properties Composite->maxValue to $vaultMax  and Composite->minValue to 0.0. This is what should appear on the display
 
*Result*
The heat map is a very direct visual alert system.  Red indicates an Alert state, Green indicates that there is no alert, and a gradient of colors in between the two extremes. The default value for CpuUsage is set to 15. Thus a red, alert state occurs when CpuUsage is 15 (15%). 

If we move the slider to higher CpuUsage, say 50 (50%), Fewer Red, Alert conditions will result. Pressing the upper right hand “New Window” button  will bring up a new window containing the heat map that can be set independently of the original window.  In this example the original window is set to CpuUsage 5, and the New Window to CpuUsage 75.
 
##Pattern 10 - Show Trend Chart of Multiple Trends      
Multiple trends from synchronous data
##Pattern 11 - Show Trend Chart of Multiple Trends      
Multiple trends from asynchronous data
##Pattern 12 - Trend time range
NOT APPROVED




