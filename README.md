
# RETS Integration

A brief description of RETS Integration






## Description of Repository

This repository contains code for extracting data using the RETS API of a company, using the node-rets package. Here are the details of how the code works:



## Usage


The code can be used for the following operations:

1. RETS Data Insertion: The code extracts data using the RETS API and inserts it into a MySQL database. It extracts metadata of the Property class, searches for entries with StandardStatus as Active, Pending, or Active Under Contract, transforms the data using the lookup_value class and metadata lookup, and stores it in the MySQL database.

2. RETS Data Updation Periodically: The code updates the data stored in the MySQL database periodically by checking if MLSListDate is today and ModificationTimeStamp is the same. If there is any difference between the entries that exist in the database and the new ones, it updates the entries in the database.


RETS Integration
This repository contains code for integrating with the RETS API of a company using the node-rets package. The code performs the following operations:

Usage
The code can be used for the following operations:

RETS Data Insertion: The code extracts data using the RETS API and inserts it into a MySQL database. It extracts metadata of the Property class, searches for entries with StandardStatus as Active, Pending, or Active Under Contract, transforms the data using the lookup_value class and metadata lookup, and stores it in the MySQL database.

RETS Data Updation Periodically: The code updates the data stored in the MySQL database periodically by checking if MLSListDate is today and ModificationTimeStamp is the same. If there is any difference between the entries that exist in the database and the new ones, it updates the entries in the database.

## Procedure
The following is the procedure followed by the code:

1. Authentication: The code uses the node-rets package to authenticate the user and connect to the RETS API.

2. Metadata Extraction: The code extracts metadata of the Property class using the metadata function of the node-rets package.

3. Data Extraction: The code searches for entries with StandardStatus as Active, Pending, or Active Under Contract using the search function of the node-rets package.

4. Data Transformation: The code transforms the data using the lookup_value class and metadata lookup to keep a record of the original data.

5. Data Storage: The code uses MySQL/FireStore to store the data.

6. Periodic Data Updation: The code updates the data stored in the MySQL database periodically using the cron package.

7. Finally, we have another function that adds media files of those ListingID that exist, as there is a different class called PROP_MEDIA.


## Dependencies

Install my-project with npm

```bash
node-rets: to connect with RETS API and extract data.
mysql: to connect with the MySQL database and store the data.
Firestore:The repository is desgined for FirestoreDB
cron: to schedule the periodic update of data.
request: to fetch media files from the server.
```
    


## Installation
1. Clone the repository
2. Install the dependencies using npm install.
3. Set up the MySQL database with the required tables.
4. Add the user credentials to the config.js file.
5. Run the code using node index.js.


## Code
**Authentication**

```javascript
const rets = require('node-rets');
const config = require('./config');

const client = new rets({
    loginUrl: config.loginUrl,
    username: config.username,
    password: config.password,
    version: config.version
});

```

**Metadata**

To extract metadata of the Property class, we use the following code:
```javascript
client.metadata('METADATA-CLASS', { 'Type': 'Property' }, (err, data) => {
    if (err) throw err;
    // code to extract metadata
});

```

**Data Extraction**

To extract data from the Property class, we use the following code:
```javascript
const query = "(StandardStatus=Active|Pending|Active Under Contract)";
client.search.query("Property", "ALL", query, {limit: 500}, (err, search) => {
    if (err) throw err;
    // code to extract data
});

```

**Data Transformation**

To transform the data, we use the lookup_value class and the metadata lookup:
```javascript
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

```



## Tech Stack


**Server:** Node, Express

**Main Dependencies:** node-rets,firebase-admin or Mysql



## API Reference

#### Get all items

```http
  client.search('Parent_class','Sub_class','DMQL2')
```



#### Get item

```http
  client.search('Parent_class','Sub_class',`ListingId={id}`)
```




## Documentation

[RETS Specifications (legacy)](https://www.reso.org/rets-specifications/#:~:text=The%20Real%20Estate%20Transaction%20Standard,nor%20is%20it%20a%20language.)

