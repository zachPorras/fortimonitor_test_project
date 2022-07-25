# fortizach (created with Vue2 using Vuetify components)

![image](https://user-images.githubusercontent.com/70177842/180697969-d77b8cb6-d201-4003-93d6-4117e647850d.png)


## Requirements
- Node.js
- NPM
- Git (for repo interaction)

### Run app locally
```
npm run serve
```

### Purpose
This application was created as a test project for FortiMonitor.

### Functionality
Application's primary function is to display FortiMonitor outage data via a Panopta API. API endpoint offers 10 most recent outage reports which are paginated within a simple table in the UI. Table columns consist of a selection of response values for each outage.

Panopta API call occurs when UI view loads via Vue mounted() hook. API call is continuously made every 30 seconds once view rendered to display most up-to-date data from API. Most recent successful API call date & time are displayed in subtitle of table for user reference.

Loading state is represented to user via a Vuetify spinner component which appears beneath the table while app awaits response from API call, signaling that data in the table will be udpated shortly.

Failed API calls result in a red error message which appears beneath table with error details (10 second timeout on error visibility). This also handles errors with API call made during initial rendering of app view. 
![image](https://user-images.githubusercontent.com/70177842/180698040-c2385236-5e44-4106-80a1-da00bd0bcc55.png)
If data from a previous API call already exists in table at the time of a bad API call, data remains in table until a successful API call results in a data refresh.

Data is paginated client-side since API exposure is limited to a strict set of data with no specific querying available. Response data is also very minimal which does not slow the frontend considerably during pagination.

### Future Enhancements
This application accesses the Panopta API endpoint via GET request only. If access to the API were expanded, more features could be introduced to create a more complex monitoring tool. For instance, access to all historical outage data would allow for user querying and displaying performance trends.

App is currently best viewed in large & medium screen sizes. Not yet optimized for tablet & mobile screen sizes. The following techniques would allow for better responsivness:
- Incorporation of CSS media queries for component sizing
- Enhancements to CSS flexbox usage
- Creating a more responsive custom table component instead of using a Vuetify table component

