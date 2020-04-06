# Email Service API
API that allows you to create email templates, send notifications, or send emails using your saved email templates

### Prerequisites

  - Apache Maven 3+
  - [Martini Desktop](https://www.torocloud.com/martini/download)

### Building the Martini Package

```
$ mvn clean package
```
This will create a ZIP file named `email-service-api-bin.zip` containing all the files (services, configurations, etc.) needed under the `target` folder. This ZIP file is what we call a [Martini Package](https://docs.torocloud.com/martini/latest/developing/package/) which then you can import in Martini Desktop to get started. You can learn more how to import a Martini Package by visiting our [documentation](https://docs.torocloud.com/martini/latest/developing/package/importing/)

### Getting the Martini Package from TORO Marketplace

You can also get this package via TORO Marketplace. See our documentation on [How to import a Martini Package from Marketplace](https://docs.torocloud.com/martini/latest/developing/package/importing/#from-the-marketplace).

### Usage
This package exposes operations for a simple email client REST API that allows you to do send emails. You can find the [Gloop REST API](https://docs.torocloud.com/martini/latest/developing/gloop/api/rest/) file at `/api/EmailService` under the `code` folder after importing the package to your Martini Desktop application.

### Setup
This Martini Package automatically sets up the required resources for you. During the package startup, Martini will execute the configured _Startup Service_ that initializes the database to be used for storing the record that will be creating for using this mock api.

This package uses HSQL as the default datastore but you can change this to MySQL by updating the [package properties](https://docs.torocloud.com/martini/latest/developing/package/properties/) located at [conf](https://docs.torocloud.com/martini/latest/developing/package/directory/) folder. Below is what the contents of the properties file look like

```
# Database to use
database.type=hsql

# Database name
database.name=email_template

# MYSQL database configuration. Optional.
# Use MYSQL as the datasource instead of the default HSQLDB.

mysql.driver=com.mysql.cj.jdbc.Driver
mysql.connection.url=jdbc:mysql://<host>?allowMultiQueries=true&sessionVariables=sql_mode='ANSI'
mysql.username=root
mysql.password=

# HSQL database configuration. Default.
# use HSQL as the datasource.

hsql.driver=org.hsqldb.jdbc.JDBCDriver
hsql.connection.url=jdbc:hsqldb:file:${toroesb.home}data/hsql/email_template.db;hsqldb.tx=MVCC;sql.syntax_mys=true
hsql.username=sa
hsql.password=

# Postgres database configuration
postgres.driver=org.postgresql.Driver
postgres.connection.url=jdbc:postgresql://<host>:<port>/<database-to-use?currentSchema=<schema-to-use>
postgres.username=postgres
postgres.password=

# Oracle database configuration
oracle.driver=oracle.jdbc.driver.OracleDriver
oracle.connection.url=jdbc:oracle:thin:@<host>:<port>:<sid>
oracle.username=
oracle.password=

# MS SQL database configuration
mssql.driver=com.microsoft.sqlserver.jdbc.SQLServerDriver
mssql.connection.url=jdbc:sqlserver://<host>:<port>;databaseName=<database-to-use>;
mssql.username=sa
mssql.password=

# SMTP Server Settings
smtp.server=smtp.gmail.com
smtp.username=
smtp.password=
smtp.port=465
smtp.email.alias=

# Email Notification Recipients
email.notification.recipients=
```
* `debug.enabled` when set to `true` initializes the database with dummy data when the package starts. The initialized data are also removed when the package stops or is unloaded. Set the value of this property to `false` if you want to keep your data when doing a restart. You can also set this property to `true` when the package starts and then set to `false` afterwards so that you'll have the data initialized on startup, and keep the data when the package or the Martini instance do a restart
* `database.type` sets the database provider the Martini package will use. If you will use the default hsql config, you don't need to change anything in the hsql configuration. **Note**: If you will use a different hsql database, make sure that you add `sql.syntax_mys=true` in the connection properties. This ensures that the SQL query from the SQL Services in this package will be compatible with hsql.
* SMTP Server Settings - Contains your SMTP server credentials that will be used for sending emails
* `email.notification.recipients` - A comma separated list of email address that will receive the email notifications when a notification is triggered

### Operations

The base url is `<host>/api/email-service` where `host` is the location where the Martini instance is deployed. By default, it's `localhost:8080`.

#### Email Template

Contains operations related to creating, updating, or managing your email templates

`GET /templates`

Returns a list of saved email templates

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/email-service/templates \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "OK",
    "message": "OK",
    "warnings": [],
    "emailTemplate": [
        {
            "id": "55283a8b-8270-4efb-a8d1-0c38a278cf10",
            "email_template_name": "Table Template",
            "email_template_description": "A simple table",
            "email_template": "<html> <body> <table border=\"1\"> <tr> <td bgcolor=\"#CCCCCC\">$!v1</td><td bgcolor=\"#CCCCCC\">$!v1</td></tr><tr> <td bgcolor=\"#FFFFFF\">$!v2</td><td bgcolor=\"#FFFFFF\">$!v2</td></tr><tr> <td bgcolor=\"#CCCCCC\">$!v3</td><td bgcolor=\"#CCCCCC\">$!v3</td></tr><tr> <td bgcolor=\"#FFFFFF\">$!v4</td><td bgcolor=\"#FFFFFF\">$!v4</td></tr></table> </body></html>",
            "dateCreated": 1585169047000,
            "dateUpdated": 1585169047000
        }
    ]
}
```

`POST /templates`

Creates a new email template

**Note**

The velocity properties in your HTML template should follow this format: `$!propName`

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/email-service/templates \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "email_template_name": "Table Template",
    "email_template_description": "A simple table",
    "email_template": "<html> <body> <table border=\"1\"> <tr> <td bgcolor=\"#CCCCCC\">$!v1</td><td bgcolor=\"#CCCCCC\">$!v1</td></tr><tr> <td bgcolor=\"#FFFFFF\">$!v2</td><td bgcolor=\"#FFFFFF\">$!v2</td></tr><tr> <td bgcolor=\"#CCCCCC\">$!v3</td><td bgcolor=\"#CCCCCC\">$!v3</td></tr><tr> <td bgcolor=\"#FFFFFF\">$!v4</td><td bgcolor=\"#FFFFFF\">$!v4</td></tr></table> </body></html>"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `201` with the response payload similar below.
```
{
    "result": "OK",
    "message": "OK",
    "warnings": [],
    "emailTemplate": {
        "id": "55283a8b-8270-4efb-a8d1-0c38a278cf10",
        "email_template_name": "Table Template",
        "email_template_description": "A simple table",
        "email_template": "<html> <body> <table border=\"1\"> <tr> <td bgcolor=\"#CCCCCC\">$!v1</td><td bgcolor=\"#CCCCCC\">$!v1</td></tr><tr> <td bgcolor=\"#FFFFFF\">$!v2</td><td bgcolor=\"#FFFFFF\">$!v2</td></tr><tr> <td bgcolor=\"#CCCCCC\">$!v3</td><td bgcolor=\"#CCCCCC\">$!v3</td></tr><tr> <td bgcolor=\"#FFFFFF\">$!v4</td><td bgcolor=\"#FFFFFF\">$!v4</td></tr></table> </body></html>",
        "dateCreated": 1585169047000,
        "dateUpdated": 1585169047000
    }
}
```

`PUT /templates`

Updates an email template. The id of the email template you are going to update must be included in the body of the request

**Sample Request**

**curl**
```
curl -X PUT \
  http://localhost:8080/api/mock-timesheet-api/users \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "id": "55283a8b-8270-4efb-a8d1-0c38a278cf10",
    "email_template_name": "Updated Template Name",
    "email_template_description": "A new description"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload similar below
```
{
    "result": "OK",
    "message": "Successfully updated email template with id: 55283a8b-8270-4efb-a8d1-0c38a278cf10",
    "warnings": []
}
```

`GET /templates/<templateId>`

Retrieves a single email template record that matches the given `temlplateId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/email-servicei/users/55283a8b-8270-4efb-a8d1-0c38a278cf10 \
  -H 'Accept: application/json'
```

**Sample Response** 

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "OK",
    "message": "OK",
    "warnings": [],
    "emailTemplate": {
        "id": "55283a8b-8270-4efb-a8d1-0c38a278cf10",
        "email_template_name": "Updated Template Name",
        "email_template_description": "A new description",
        "email_template": "<html> <body> <table border=\"1\"> <tr> <td bgcolor=\"#CCCCCC\">$!v1</td><td bgcolor=\"#CCCCCC\">$!v1</td></tr><tr> <td bgcolor=\"#FFFFFF\">$!v2</td><td bgcolor=\"#FFFFFF\">$!v2</td></tr><tr> <td bgcolor=\"#CCCCCC\">$!v3</td><td bgcolor=\"#CCCCCC\">$!v3</td></tr><tr> <td bgcolor=\"#FFFFFF\">$!v4</td><td bgcolor=\"#FFFFFF\">$!v4</td></tr></table> </body></html>",
        "dateCreated": 1585169047000,
        "dateUpdated": 1585170386000
    }
}
```

`DELETE /templates/<templateId>`

Deletes an email template that matches the given `templateId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/templates/55283a8b-8270-4efb-a8d1-0c38a278cf10 \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "OK",
    "message": "Successfully deleted email template",
    "warnings": []
}
```

#### Sending an email

Endpoint for sending an email via HTTP

`POST /send`

**Full Payload Representation**
```
{
    "recipients": [
        {
            "to": ["jdoe@mail.com"],
            "cc": [],
            "bcc": []
        }
    ],
    "velocityProperties": {
        "v1": "value 1",
        "v2": "value 2",
        "v3": "value 3",
        "v4": "value 4"
    },
    "emailTemplateId": "55283a8b-8270-4efb-a8d1-0c38a278cf10",
    "subject": "Sample Email",
    "body": ""
}
```

**Notes**

* `velocityProperties` is a dynamic payload. The key-value pair for this property must match the name of the variables you defined in your HTML template
* The `body` property is optional. If an `emailTemplateId` is provided, do not include this property when sending an email. Alternatively, if you just want to send a plain email, you can exclude the `velocityProperties` and `emailTemplateId` properties in the request

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/email-service/send \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json \
  -d '{
    "recipients": [
        {
            "to": ["jdoe@mail.com"]
        }
    ],
    "velocityProperties": {
        "v1": "value 1",
        "v2": "value 2",
        "v3": "value 3",
        "v4": "value 4"
    },
    "emailTemplateId": "5f065246-90da-4153-9008-3613859a87ff",
    "subject": "Sample Email"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.
```
{
    "status": "Email/s sent."
}
```

To verify if the email message has been sent to the email address you provided in the request, check the recipient's inbox.

#### Sending notifications via email

`POST /email-service/notifications`

Sends an email notification to recipients configured in `email.notification.recipients` package property.

**Payload**

This endpoint will accept any kind of JSON data. Additionally, you can modify the JMS listener that listens to the JMS message being published by the service this endpoint invokes e.g. modify the queue/topic name.

When sending the request, an optional `queue` header can be provided if you want your notification message to be published elsewhere. Default value for this header when not provided is `notifications`

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/email-service/notfications \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "message": "This is a notification!"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `202`. This endpoint does not return any response payload as messages might be processed at a later time.
