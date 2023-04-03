README
This repository contains code for extracting data using the RETS API of a company, using the node-rets package. Here are the details of how the code works:

Usage
First, the user credentials need to be added to the code to authenticate the user and connect to the API.
After authentication, the metadata of the Property class is extracted to start working on it.
We use DMQL2 queries to find entries in the Property class that have StandardStatus as Active, Pending, or Active Under Contract.
The entries in the Property class have many values that are alphanumeric, so we need to keep a record of their original data. To achieve this, we create another class for each class name as lookup_value, where we make a JSON of that and loop through each entry and each key-value pair to cross-check it with metadata lookup and set its corresponding value.
We also create another function that runs every 30 minutes to update the original data in the database by checking if MLSListDate is today and ModificationTimeStamp is the same. If there is any difference between the entries that exist in the database and the new ones, we update the entries in the database.
Finally, we have another function that adds media files of those ListingID that exist, as there is a different class called PROP_MEDIA.
Dependencies
node-rets: to connect with RETS API and extract data.
mysql: to connect with the MySQL database and store the data.
cron: to schedule the periodic update of data.
request: to fetch media files from the server.
Installation
Clone the repository
Install the dependencies using npm install.
Set up the MySQL database with the required tables.
Add the user credentials to the config.js file.
Run the code using node index.js.
Code
Authentication
We use the node-rets package to authenticate the user and connect to the RETS API:

php
Copy code
const rets = require('node-rets');
const config = require('./config');

const client = new rets({
    loginUrl: config.loginUrl,
    username: config.username,
    password: config.password,
    version: config.version
});
Metadata
To extract metadata of the Property class, we use the following code:

javascript
Copy code
client.metadata('METADATA-CLASS', { 'Type': 'Property' }, (err, data) => {
    if (err) throw err;
    // code to extract metadata
});
Data Extraction
To extract data from the Property class, we use the following code:

javascript
Copy code
const query = "(StandardStatus=Active|Pending|Active Under Contract)";
client.search.query("Property", "Listing", query, {limit: 500}, (err, search) => {
    if (err) throw err;
    // code to extract data
});
Data Transformation
To transform the data, we use the lookup_value class and the metadata lookup:

javascript
Copy code
const lookup = {};
client.metadata('METADATA-CLASS', { 'Type': 'lookup_value' }, (err, data) => {
    if (err) throw err;
    data.forEach((lookupData) => {
        lookup[lookupData.LookupName] = {};
        lookupData.LookupValues.forEach((lookupValue) => {
            lookup[lookupData.LookupName][lookupValue.ShortValue] = lookupValue.LongValue;
        });
    });
});

// code to transform data
Data Storage
