# fortizach

## Project created with Vue2

### Run app locally
```
npm run serve
```

### Purpose
This application was created as a test project for FortiMonitor.

### Functionality
Application's primary function is to display FortiMonitor outage data via a Panopta API. API endpoint offers 10 most recent outage reports which are paginated in UI within a simple table. Table columns consist of a specified selection of response object values for each outage.

API call occurs when UI view loads via Vue mounted() hook. API call is continuously made every 30 seconds thereafter to display most up-to-date data from API. Most recent successful API call date & time are displayed in subtitle of table for user reference.

Failed API calls result in a red error message to appear beneath table with error details (10 second timeout on error visibility). This includes the API call made during initial rendering of app view. If data already exists in table at the time of a bad API call, data remains in table until a successful API call results in a data refresh.

### Development Planning Notes
Data is paginated client-side since API exposure is limited to a strict set of data with no specific querying available. Response data is also very minimal which does not slow the frontend during pagination.

