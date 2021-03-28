# Vtiger (Laravel Package)
Use the Vtiger webservice (REST) API from within Laravel for the following operations.

- Create
- Retrieve
- Update
- Delete
- Search
- Query
- Describe

See [Third Party App Integration (REST APIs)](http://community.vtiger.com/help/vtigercrm/developers/third-party-app-integration.html)

## Installation, Configuration and Usage

### Installing
1. In order to install the Vtiger package in your Laravel project, just run the composer require command from your terminal:

    ```
    composer require "clystnet/vtiger ^1.4"
    ```
2. Publish the configuration file:

    ```
    php artisan vendor:publish --tag="vtiger"
    ```

### Configuration

- In Vtiger, create a new or select an existing user.
- Under *User Advanced Options* make note of the *username* and *access key*.
- In your application, edit *config/vtiger.php* and replace the following array values
  - Set the url to the https://{DOMAIN_NAME}/webservice.php
  - Set the username and accesskey with your CRM username and access key.
  - Set persistconnection to false if you want a fresh login with each request

    |key              |value                                |
    |-----------------|-------------------------------------|
    |url              |http://www.example.com/webservice.php|
    |username         |API                                  |
    |accesskey        |irGsy9HB0YOZdEA                      |
    |persistconnection|true                                 |
    |max_retries      |10                                   |

> Because I've experienced problems getting the sessionid from the CRM when multiple users are accessing the CRM at the same time, the solution was to store the sessionid into a file within Laravel application.
> Instead of getting the token from the database for each request using the webservice API, a check is made against the expiry time in the file. If the expiry time has expired, a token is requested from the CRM and file is updated with the new token and updated expiry time.

### Usage

In your controller include the Vtiger package
```php
use Vtiger;
```
For these examples I will use the id "13x12" which means: 13 the Contact id and 12 the Contacts module. [See all module ids](#modules)


#### Create

To insert a record into the CRM, first create an array of data to insert.
```php
$MODULE_NAME = "Contacts";

$data = array(
'assigned_user_id' => '19x1',
'lastname' => 'perez',
);

Vtiger::create($MODULE_NAME, json_encode($data));
```


#### Retrieve

To retrieve a record from the CRM, you need the id you want to find.
```php
$id = '12x13';
$obj = Vtiger::retrieve($id);
var_dump($obj);
```

#### Update

The easiest way to update a record in the CRM is to retrieve the record first.
```php
$id = '12x13';
$obj = Vtiger::retrieve($id);
```

Then update the object with updated data.
```php
$obj->result->firstname = 'Your new value';
$update = Vtiger::update($obj->result);
```

#### Delete

To delete a record from the CRM, you need the id of the record you want to delete (i.e. '12x13').
```php
$id = '12x13';
$delete = Vtiger::delete($id);
```



#### Search

This function is a sql query builder wrapped around the query function. Accepts instance of laravels QueryBuilder.
```php
$query = DB::table('Contacts')->select('id', 'firstname', 'lastname')->where('firstname', "'Jhon'");

$obj = Vtiger::search($query,false);

foreach($obj->result as $result) {
    print_r($result);
}
```
Dont forget insert Facade
```php
use Illuminate\Support\Facades\DB;
```

By default the function will quote but not escape your inputs, if you wish for your data to not be quoted, set the 2nd paramater to false like so:
```php
$obj = Vtiger::search($query, false);
```

Also keep in mind that Vtiger has several limitations on it's sql query capabilities. You can not use conditional grouping i.e "where (firstname = 'John' AND 'lastname = 'Doe') OR (firstname = 'Jane' AND lastname = 'Smith') will fail.


#### Query

To use the [Query Operation](http://community.vtiger.com/help/vtigercrm/developers/third-party-app-integration.html#query-operation), you first need to create a SQL query.
```php
$query ="SELECT id, firstname, lastname FROM Contacts WHERE firstname = 'Jaime';";
```

Then run the query...
```php
$obj = Vtiger::query($query);

foreach($obj->result as $result) {
    print_r($result);
}
```

#### Describe

To describe modules in the CRM run this with the module name

```php
$moduleDescription = (Vtiger:describe("Contacts"))->result;
```

### Modules

```
1    Campaigns
2    Invoice
3    SalesOrder
4    PurchaseOrder
5    Quotes
6    Faq
7    Vendors
8    PriceBooks
9    Calendar
10   Leads
11   Accounts
12   Contacts
13   Potentials
14   Products
15   Documents
16   Emails
17   HelpDesk
18   Events
19   Users
20   Groups
21   Currency
22   DocumentFolders
23   CompanyDetails
24   PBXManager
25   Services
26   ServiceContracts
27   SMSNotifier
28   ModComments
29   ProjectMilestone
30   ProjectTask
31   Project
32   Assets
33   LineItem
34   Tax
35   ProductTaxes
```

## Contributing

Please report any issue you find in the issues page. Pull requests are more than welcome.

## Authors

* **Adam Godfrey** - [Clystnet](https://www.clystnet.com)
* **Chris Pratt** - [Clystnet](https://www.clystnet.com)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
