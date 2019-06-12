BSD 3-Clause License

Copyright (c) 2019, Quality Technology Services, LLC 
All rights reserved.

# qts-api
QTS API Guide

Overview
QTS exposes its SDP (Service Delivery Platform) functionality via RESTful APIs.

This document is for developers wishing to automate the provisioning and management of their QTS resources, including Roster, Power, Sensor, Asset Manager and Online Ordering. It provides an overall picture of the REST API interface along with details to develop application integration code against it.

To work with SDP RESTful APIs, you can use any browser or client application that supports HTTP and HTTPS protocols. This documentation and API collections are in Postman. Each API method includes a description of the functionality as well as an example output.

You can create new API users for your company using the Roster Tool > Create new User. For any questions please contact QTS Support.

apisupport@qtsdatacenters.com

API Requests and Version Control
QTS API URLs use the following naming convention which includes the version: https://api.qtsdatacenters.com/sdp/api/{modulename}/v1/{resourcePath}

For example, in order to list user's environments, use the following URL: https://api.qtsdatacenters.com/sdp/api/roster/v1/user/envusers

The version of the RESTful API is noted in the URL.

QTS will up-version API based on major changes including:

a change in the format of the response
a change in the response/data type
removing any part of the API
Within SDP, API privilege user account must have the following roles:

API_CUSTOMER_STANDARD
ROSTER_ADMIN
SDP UI URL:
https://sdp.qtsdatacenters.com

Common API Responses
APIs use standard HTTP status codes to indicate the general result of a request. Every response from the API will have one of the following status codes in the header:

HTTP Code	Reason	Description
200 OK		Success. Indicates that the request was valid and the transaction executed normally.
201 Created		Indicates that a new resource was created. 201 Created is not used when a resource is updated.
400 Bad Request	INVALID_INPUT_DATA	Indicates that the JSON posted with the request contained bad syntax or was missing required fields.
401 Unauthorized	AUTHORIZATION_FAILURE	Indicates invalid credentials were provided for authentication.
403 Forbidden	FORBIDDEN	Indicates that the credentials provided for authentication were valid but the user is not permitted to access the resource.
404 Not Found	RESOURCE_NOT_FOUND	Indicates that there is no resource at the URI specified in the request.
405 Method Not Allowed		Indicates the URL is valid but operation is not supported or not allowed for the resource.
408 Request timeout		Indicates the request response was not received during expected amount of time.
500 Internal Server Error	UNEXPECTED_ERROR	Indicates that a general error has occurred with the request that is not described by another status code.
503 Service Unavailable		System is down for scheduled maintenance.This response remains in place for the duration of the scheduled maintenance window.
400 Bad Request	INVALID_INPUT_DATA	Indicates input data does not correspond to validation rules or mandatory fields were missed.
Paging, Filtering and Sorting
Paging is the act of splitting a large list of results into consumable chunks as a means of improving performance and general manageability.

Paging operators are separated with & symbol
Paging operators are case sensitive
The following operators are supported:

pageNumber - specify number of pages
pageSize - number of records per page
Example: /sdp/ticketing/v1/api/temporary_visitor_reservations?pageNumber=1&pageSize=20

Optional Paging Parameters
Parameter	Usage	Example
pageSize	Optional. May only be specified once. Must be a positive integer between 1 and a maximum value documented for each API function.	pageSize=50 Return 50 records per page
pageNumber	Optional. May only be specified once. Must be a positive integer. If the value supplied exceeds the number of pages for the request, the last page will be returned.	pageNumber=3 Returns the 3rd page. If pageSize is 50, then return records 101-through-150.
JSON response elements when using paging parameters:

Attribute Name	Attribute Details	Description
pageCount	Non-negative Integer	The number of result records in this page.
pageNumber	Non-negative Integer	The page number requested by the user or the default page (1) if none supplied.
pageSize	Non-negative Integer	The page size used; either specified in the request or the default for the API function being requested, max value 10000.
totalCount	Non-negative Integer	The total number of result records matching request criteria
Example result:
{
"pageInfo": {
"pageSize": 20,
"totalCount": 115,
"totalPages": 6,
"pageNumber": 1
},

Filtering and Sorting
Use sorting to present results in a specified order.

Sort keyword specifies the parameter results should be sorted.
Name of request parameter must be specified to identify how results should be sorted
Sorting operators:
ASC
DESC
Sorting operator and keywords are case sensitive
Example: /sdp/ticketing/v1/api/temporary_visitor_reservations?sort=location.ASC

Use filtering to reduce size of list by applying request criteria to narrow the matched set of results.

The following general rules apply across all APIs developed with the paging and filtering approach:

Every GET method has its own set of filter parameters documented
Parameter names and some parameter values are based on keywords and field names, which will be published in the API documentation
Field names are case sensitive
The set of field names and filters for a given API function can be enhanced over time with the publication of new values
Multiple filtering operators are separated with & symbol
Dates are defined in XSD format but can be passed in full (including time) or short (date only) format. For example; "20180901T00:00:00Z" and "2018-09-01" equate to the same value and time zone.
Both EQUALS (single value) and IN (multiple value) queries are supported
The filter component has the following format: ?_s=field==value where the filter criteria are specified as field=value pairs.
Logical Operators	Syntax	Description
AND	;(Semicolon)	All conditions in the filter parameter must be true. Example:?_s=firstName==john;lastName==lee Returns Roster Users with first name as "john" AND last name as "lee"
OR	,(Comma)	Any of the conditions specified in the filter parameter must be true. Example: ?_s=firstName==john,lastName==lee Returns Roster Users with first name as ‘john’ OR last name as ‘lee’
Comparison Operators	Syntax	Description
EQUAL	==	The result must be equal to the value specified in the field=value pair. Example: ?_s=username==john.test Returns only Roster Users that have ‘john.test’ as username
NOT EQUAL	!=	The result must not be equal to the value specified in the field=value pair. Example: ?_s=username!=john.test Returns only Roster Users that do not have ‘john.test’ as username
GREATER THAN	gt	The result must be greater than the value specified in the field=value pair.?_s=dateCreation=gt=2018-09-01 Returns only records created after 1st of September 1, 2018
LESS THAN	lt	The result must be less than the value specified in the field=value pair.?_s=dateCreation=lt=2018-09-01 Returns only records created before 1st of September 1, 2018
Additional Operators	Syntax	Description
Wildcard	*	The “*” wildcard character (asterisks) can be used to match zero or more characters. Each API supporting wildcard states that it supports wildcard within its own definition.
STARTS WITH	==	Example: ?_s=username==john Returns only Roster Users that has username starts with "john"
ENDS WITH	==*	Example: ?_s=username==*john Returns only Roster Users where username ends with "john"
CONTAINS	==*value*	Example: ?_s=username==*john* Returns only Roster Users that has "john" in username
ORDER BY	orderBy=value	?orderBy=username Results would be sorted by username, ascending order by default
PAGING PARAMETERS	pageNumber=value;pageSize=value	?pageNumber=2;pageSize=10 Returns only second page, 10 results per page
Sample Formatting Requests
Request	Response
/sdp/api/roster/v1/users	Lists 100 Users that are available for the current requester, sorted by username
/sdp/api/power/v1/raw_data?site=DFW1&format=json& _s=period==monthly;date==10-01-2018	Generates JSON raw power data report for monthly period October 2018. Report contains all circuits and poles of DFW1 site including kW readings for poles of specified location circuits
/sdp/api/power/v1/power_demand?site=DFW1&_s=date==12-01-2018;period==weekly;site=DFW1	Generates .xlsx report for week starting December 1, 2018 for DFW1 site
/sdp/api/roster/v1/users?pageNumber=2;pageSize=100;orderBy=date	Indicates a page size of 100 and requests the second page of results being page 101 to 200 inclusive, ordered by date of deployment ascending (default)
Paging and Filtering Common API Responses
HTTP Code	Reason	Description
400 (Error)	INCORRECT_PAGING_PARAMETER	A supplied value for any paging parameter is outside of accepted value set. A negative or non-numeric pageSize or pageNumber, pageSize too large, an invalid orderBy value or more than one value was supplied for a given paging parameter.
400 (Error)	INVALID_FILTER_VALUE	A filter was specified with an invalid value. Returned if an invalid comparative operator is used with a filter field. For example, MIN and MAX will typically not be allowed with String fields.
400 (Error)	UNSUPPORTED_PARAMETER	Invalid comparative operator
400 (Error)	INVALID_INPUT_DATA	Not a comparison expression. Returned in case any filtering or sorting parameter was missed or extra symbols added to the filter line.
400 (Error)	TOO_MANY_FILTER_PARAMETERS	Exceeded maximum supported number of filter operators. Maximum number of filter operators is 10. Returned if more than 10 values for a filter line were supplied.
Language
API Authentication

Authentication and Authorization
QTS API methods are implemented with OAuth2.0 authentication protocol support:

Username/Password + Client ID/Secret (aka API access key and secret key)
Cient account should be created for every user
Client's secret key may be regenerated
The generated token and refresh token has a finite lifetime
The lifetime of the tokens will be returned in the response of the POST token request
Token Request
The client application obtains an access token by sending a POST request to the token request endpoint with the following request parameters:

Endpoint:  /sdp/api/token/access  
Header:  Content-Type: application/json  
Body: 
{
"api_access_key": “%API Access Key%”, "api_secret_key": “%API Secret Key%”, "username": “%User name%”, "password": “%Password%
"  
}

Request Type: HTTP POST  

Response details:  
{  
   "scope": "memberOf nucleus_privileges uid"  
   "expires_in": 599  
   "token_type": "Bearer"  
   "refresh_token": "4c1fa679-5548-4c13-8726-e8be782b7ca0"  
   "access_token": "33c4b2ff-1111-498e-94e1-ca1ba0ea8ce8"    
}
POST Get Access Token
{{url}}/sdp/api/token/access
To execute the QTS API methods, you must include the Authentication Token obtained from this response as part of the Authorization parameter. The Authentication Token must be placed after the Bearer parameter with a space between the Bearer label and your Authentication Token. Generated token lifetime is returned in the response of this API.

HEADERS
Content-Typeapplication/json
BODY
{
"username":"test_api_user",
"password":"yourpasswordgoeshere",
"api_access_key":"CM6XY0UPZHASLPWJMIYY",
"api_secret_key":"I@AsGlWcSqkDqvybrxPeGX_(}!kYzEZA(E@[nQBn"
}


Example Request
Get Access Token
curl --location --request POST "https://uatmicroserv.qtsdatacenters.com/sdp/api/token/access" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer 2bfe3123-6f5d-46ef-b0d3-ad17af33c928" \
  --data "{
\"username\":\"test_api_user\",
\"password\":\"yourpasswordgoeshere\",
\"api_access_key\":\"CM6XY0UPZHASLPWJMIYY\",
\"api_secret_key\":\"I@AsGlWcSqkDqvybrxPeGX_(}!kYzEZA(E@[nQBn\"
}"
Example Response
－
{
  "access_token": "aaa2133b-bf2d-4f6b-a8ff-b9e72d603315",
  "refresh_token": "23d518e9-d1e2-459f-9827-bf97d28c3850",
  "scope": "memberOf nucleus_privileges uid",
  "token_type": "Bearer",
  "expires_in": 1799
}
POST Get Refresh Token
{{url}}/sdp/api/token/refresh
The web server OAuth authentication flow and user-agent flow both provide a refresh token that can be used to obtain a new access token. Refresh token may be provided during authorization that can be used to get a new access token. Generated refresh token lifetime is returned in the response of this API.

HEADERS
Content-Typeapplication/json
AuthorizationBearer

Example Request
Get Refresh Token
curl --location --request POST "{{url}}/sdp/api/token/refresh" \
  --header "Content-Type: application/json" \
  --data ""
API General

These API responses are necessary to obtain values to be used as required inputs for specific API calls.

For example, the SalesForce contract id is a required field for a crossConnect order.

This section also contains WADL for Roster and Power.

GET Get List of Contracts
{{url}}/sdp/ordering/v1/api/contracts
Returns list of your contracts.

In other APIs, salesForceContractId and contract location are required parameters to create a new order using /sdp/ordering/v1/api/ccx/orders/

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get list of Contracts
curl --location --request GET "{{url}}/sdp/ordering/v1/api/contracts" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "salesForceContractId": "800f2000001fnVdAAI",
    "number": "00007127",
    "gpNumber": "Test.12346.Test",
    "location": "CHI1",
    "startDate": "05-01-2018",
    "endDate": "04-30-2019"
  },
  {
    "salesForceContractId": "8002F0000003oSIQAY",
GET Get List of Data Centers
{{url}}/sdp/api/roster/v1/users/available_locations
Returns a list of data centers (sites/locations) for your Environment. Data center site id (aka location) will be used as a parameter in many of the QTS API requests.

Supported filters:

environmentName - narrows the results to locations associated with specified Environments only
state - returns Locations within the specified geographical state
city - returns Locations with specified city
locationName - returns Locations with specified Name. Case-sensitive parameter
Paging filters are returned in response.

Example to list your data centers in Georgia:
/sdp/api/roster/v1/users/available_locations?_s=state==GA

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get list of permitted Data Centers
curl --location --request GET "{{url}}/sdp/api/roster/v1/users/available_locations" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "totalCount": 9,
  "pageNumber": 1,
  "pageSize": 20,
  "pageCount": 9,
  "associatedLocations": [
    {
      "locationName": "ATL1",
      "city": "Atlanta",
      "state": "GA"
    },
GET Get Audit Logging Details
{{url}}/sdp/api/roster/v1/audit_records
Retrieves audit logs for permitted environments to check applied actions and troubleshoot request/response details.

The following basic information is audited:

userId - requester user identifier
timerequested - ISO 8601 datetime in UTC: yyyy-MM-dd'T'HH:mm:ss'Z'
resource - basic path request was sent to Example: api/device/, api/roster/user/, etc
operation - REST operation "PUT", "GET", "POST" or "DELETE" standing correspondingly for Create, Read, Update or Delete
status - HTTP status code. Example: 200 OK, 400 Error, 5xx status codes may not be logged in case when error cause is outside of the application code scope (application container failure or proxy server failure)
query - HTTP request query: all parameters in the request
payload - JSON representation of the operation-connected entity provided in HTTP request
Note: POST requests for authentication token generation are not audited because of security reasons.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get Audit Logging Details
curl --location --request GET "{{url}}/sdp/api/roster/v1/audit_records" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
    "totalCount": 16420,
    "pageNumber": 1,
    "pageSize": 20,
    "pageCount": 20,
    "auditRecords": [
        {
            "username": "portal.demo",
            "timeRequested": "2016-11-18 12:30:21.593 +0000",
            "resource": "/sdp/ui/roster/users/test/customer",
            "operation": "PUT",
GET Roster WADL
{{url}}/sdp/api/roster/v1?_wadl
Returns WADL content for User Management

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
_wadl

Example Request
Roster WADL
curl --location --request GET "{{url}}/sdp/api/roster/v1?_wadl" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
<application xmlns="http://wadl.dev.java.net/2009/02" xmlns:xs="http://www.w3.org/2001/XMLSchema">
    <grammars></grammars>
    <resources base="https://uatmicroservices.qtsdatacenters.com/sdp/api/roster/v1">
        <resource path="/users">
            <method name="GET">
                <request>
                    <param name="_s" style="query" type="xs:string"/>
                </request>
                <response>
                    <representation mediaType="application/json"/>
                </response>
GET Power WADL
{{url}}/sdp/api/power/v1?_wadl
Returns WADL content for Power APIs

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
_wadl

Example Request
Power WADL
curl --location --request GET "{{url}}/sdp/api/power/v1?_wadl" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
<application xmlns="http://wadl.dev.java.net/2009/02" xmlns:xs="http://www.w3.org/2001/XMLSchema">
    <grammars></grammars>
    <resources base="https://uatmicroservices.qtsdatacenters.com/sdp/api/power/v1">
        <resource path="/">
            <resource path="power_demand">
                <method name="GET">
                    <request>
                        <param name="_s" style="query" type="xs:string"/>
                        <param name="site" style="query" type="xs:string"/>
                    </request>
                    <response>
Service Desk Request

GET Get List of Incidents
{{url}}/sdp/ticketing/v1/api/incidents?state=Active&location=All Sites
Lists Service Desk incidents.

Supported filters:

incidentNumber
location
caller
shortDescription
description
priority
state
New
Active
Awaiting Evidence
Awaiting Maintenance
Awaiting Problem
Awaiting User Infor
Resolved
impact
Minor/Localized
Moderate/Limited
Significant/Large
Extensive/Wide Spread
urgency
Low
Medium
High
Critical
intervalStartDate (MM-DD-YYYY format)
intervalEndDate (MM-DD-YYYY format)
Notes:

Fileter values are case insensitive and use 'contains' comparison operator
Filters return incidents that have dateOpened no later then intervalEndDate and dateClosed not earlier then intervalStartDate
Paging filters are used in API results; ensure that your paging size is correct.
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
stateActive
locationAll Sites

Example Request
Get List of Incidents
curl --location --request GET "{{url}}/sdp/ticketing/v1/api/incidents?state=Active&location=All%20Sites" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "pageInfo": {
    "pageSize": 20,
    "totalCount": 1,
    "totalPages": 1,
    "pageNumber": 1
  },
  "data": [
    {
      "caller": "Test User",
      "dateOpened": "2018-10-22 17:08:00.000+0000",
GET Get List of Requested Items
{{url}}/sdp/ticketing/v1/api/requested_items?intervalStartDate=04-02-2019
Lists Service Desk requested items.

Supported filters:

location (exact match, case insensitive search)
requestedItemNumber (contains, case insensitive search)
state (contains, case insensitive search)
stage (contains, case insensitive search)
intervalStartDate (MM-DD-YYYY format)
intervalEndDate (MM-DD-YYYY format)
Notes:

Filters return incidents that have dateOpened no later then intervalEndDate and dateClosed not earlier then intervalStartDate
Paging filters are used in API results; ensure that your paging size is correct
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
intervalStartDate04-02-2019

Example Request
Get List of Requested Items
curl --location --request GET "{{url}}/sdp/ticketing/v1/api/requested_items?intervalStartDate=04-02-2019" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
    "pageInfo": {
        "pageSize": 20,
        "totalCount": 5,
        "totalPages": 1,
        "pageNumber": 1
    },
    "data": [
        {
            "requestedItemNumber": "RITMXXXXXXX",
            "dateOpened": "2019-04-02 14:38:00.000+0000",
GET Get List of Temporary Reservations
{{url}}/sdp/ticketing/v1/api/temporary_visitor_reservations
Lists temporary Reservations

Supported filters:

reservationNumber (ASCII printable, max 100 symbols, contains character search, case insensitive)
intervalStartDate (MM-DD-YYYY format)
intervalEndDate (MM-DD-YYYY format)
location (ASCII printable, max 40 symbols, data center code, exact match search, case insensitive)
firstName (ASCII printable, max 40 symbols, contains character search, case insensitive)
lastName (ASCII printable, max 40 symbols, contains character search, case insensitive)
company (ASCII printable, max 40 symbols, contains character search, case insensitive)
By default all temporary reservations are returned

Example filters:

pageNumber=1
pageSize=20
intervalStartDate=05-01-2018
intervalEndDate=05-30-2018
sort=location%3ADESC
location=MIA1
firstName=John
lastName=Smith
company=demo
?pageNumber=1&pageSize=20&intervalStartDate=05-01-2018&intervalEndDate=05-30-2018&sort=location%3ADESC&location=mia1&firstName=John&lastName=Smith&company=demo

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get List of Temporary Reservations
curl --location --request GET "{{url}}/sdp/ticketing/v1/api/temporary_visitor_reservations" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
    "pageInfo": {
        "pageSize": 20,
        "totalCount": 42,
        "totalPages": 3,
        "pageNumber": 1
    },
    "data": [
        {
            "reservationNumber": "RES0049242",
            "location": "SUW1",
POST Create New Incident
{{url}}/sdp/ticketing/v1/api/incidents
Create a new incident or request.

Supported parameters:

onBehalfOf (username of the user who would be set as caller, related to the same Company)
impact
Minor/Localized
Moderate/Limited
Significant/Large
Extensive/Wide Spread
urgency
Low
Medium
High
Critical
shortDescription (required, max 160 symbols)
longDescription (max 4000 symbols)
previousIncident (true / false)
previousIncidentNumber (required in case previousIncident set to true)
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"onBehalfOf":"portal.demo.api",
"impact":"Minor/Localized",
"urgency":"Low",
"shortDescription":"short description for incident created through API"
}


Example Request
Create New Incident
curl --location --request POST "{{url}}/sdp/ticketing/v1/api/incidents" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"onBehalfOf\":\"portal.demo.api\",
\"impact\":\"Minor/Localized\",
\"urgency\":\"Low\",
\"shortDescription\":\"short description for incident created through API\"
}"
Example Response
201 － Created
{
  "caller": "DemoAPI",
  "dateOpened": "2019-04-25 18:20:00.000+0000",
  "environment": "",
  "location": "",
  "shortDescription": "short description for incident created through API",
  "description": "",
  "priority": "Low",
  "state": "New",
  "impact": "Minor/Localized",
  "urgency": "Low",
POST Request Temporary Access
{{url}}/sdp/ticketing/v1/api/temporary_visitor_reservations/request
Request temporary access to QTS Data Centers for someone who is not an SDP user (not set up in Roster).
Returns temporary reservation number that can be verified by physical secuirity team.
Parameter value must contain information about the person and details of the requested access.

Example:

/sdp/ticketing/v1/api/temporary_visitor_reservations?intervalStartDate=05-30-2018&intervalEndDate=05-30-2018&sort=location.DESC&location=mia1&firstName=John&lastName=Smith&company=demo

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"reasonForVisit":"Visit reason explanation",
"firstName":"John",
"lastName":"Smith",
"middleName":"",
"company":"demo",
"workPhone":"8888888",
"mobilePhone":"8888888",
"homePhone":"8888888",
"email":"example@example.com",
"location":"CHI1",
"startDate": "05-28-2018",
"endDate": "05-29-2018",
"timeRangeStart": "00:00",
"timeRangeEnd": "23:59"
}


Example Request
Request Temporary Access
curl --location --request POST "{{url}}/sdp/ticketing/v1/api/temporary_visitor_reservations/request" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"reasonForVisit\":\"Visit reason explanation\",
\"firstName\":\"John\",
\"lastName\":\"Smith\",
\"middleName\":\"\",
\"company\":\"demo\",
\"workPhone\":\"8888888\",
\"mobilePhone\":\"8888888\",
\"homePhone\":\"8888888\",
\"email\":\"example@example.com\",
\"location\":\"DFW1\",
\"startDate\": \"05-28-2018\",
\"endDate\": \"05-29-2018\",
\"timeRangeStart\": \"00:00\",
\"timeRangeEnd\": \"23:59\"
}"
Example Response
201 － Created
{
  "message": "New reservation was successfully created. Reservation Number: RES0121117",
  "code": "CREATED"
}
POST Request Cancellation of Temporary Access
{{url}}/sdp/ticketing/v1/api/temporary_visitor_reservations/RES0001671/cancel
Creates request to cancel temporary reservation. Reservation number (RES#######) must be specified in URL parameter.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json

Example Request
Request Cancellation of Temporary Access
curl --location --request POST "{{url}}/sdp/ticketing/v1/api/temporary_visitor_reservations/RES0001671/cancel" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data ""
POST Request Badge Access 
{{url}}/sdp/api/roster/v1/users/<target username>/request_badge_access
Requests new badge access or reuse the same badge number for an additional location. The badge user must have access to the data center.

Target Username – the username for whom the badge is being requested must be specified in the URL.
Description field should be filled with comments to specify date, time and additional details for the badge assignment.
To reuse badge from other site – the site attribute ID must be specified as “reuseBadgeSiteAttributeId”.
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"locationName": "AM1",
"reuseBadgeSiteAttributeId": "1234567",
"description": "please request new badge for this user"
}


Example Request
Request Badge Access
curl --location --request POST "{{url}}/sdp/api/roster/v1/users/%3Ctarget%20username%3E/request_badge_access" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"locationName\": \"AM1\",
\"reuseBadgeSiteAttributeId\": \"1234567\",
\"description\": \"please request new badge for this user\"
}"
PUT Update Incident
{{url}}/sdp/ticketing/v1/api/incidents/INC0547767
Update an existing incident. Incident number (INC#######) must be specified as request URL parameter. Only list the parameters that you want to update in the body.

Supported parameters:

onBehalfOf (username of the user who would be set as caller, must be related to the same Company)
impact
Minor/Localized
Moderate/Limited
Significant/Large
Extensive/Wide Spread
urgency
Low
Medium
High
Critical
shortDescription (required, maximum 160 characters)
longDescription (maximum 4000 characters)
previousIncident (true / false)
previousIncidentNumber (required if previousIncident set to true)
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
    "caller": "demouser",
    "dateOpened": "2019-02-20 13:44:00.000+0000",
    "environment": "",
    "location": "",
    "shortDescription": "short description for incident created through API_updated",
    "description": "",
    "priority": "Critical",
    "state": "New",
    "impact": "Moderate/Limited",
    "urgency": "Critical",
    "incidentNumber": "INC0482524"
}


Example Request
Update Incident
curl --location --request PUT "{{url}}/sdp/ticketing/v1/api/incidents/INC0547767" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
   
    \"impact\": \"Moderate/Limited\"
   
}"
Example Response
200 － OK
{
  "caller": "Portal DemoAPI",
  "dateOpened": "2019-04-23 16:49:00.000+0000",
  "environment": "",
  "location": "",
  "shortDescription": "short description for incident created through API",
  "description": "",
  "priority": "Low",
  "state": "Active",
  "impact": "Moderate/Limited",
  "urgency": "Low",
Power

GET Get Raw Power Data (pole level)
{{url}}/sdp/api/power/v1/raw_data/poles?site=SUW1
Retrieves actual kW and amp readings on pole level for a specified location.

Report periods:

Daily: raw readings for every 15 minutes on pole level
Weekly: calculated average for every 1 hour on pole level
Monthly: calculated average for every 1 day on pole level
Supported filters:

site

Required parameter
Can be specified only once
Specified as query parameter: ?site=%siteName%
If no filters from the list ( spaceType, spaceName, circuit, pole) are specified, then the raw data for the whole site will be extracted
Raw data for deployed poles that belong to the specified site and Customer Company checked for an exact match
date

Optional parameter
Can be specified only once
Report will contains data for the specified date only
If not specified - report contains data for current date only
Date format: YYYY-MM-DD format
period

Optional parameter
Can be specified once
Supported values
daily - default value
For poles: 15 minutes raw data
For circuits/spaces: 15 minutes raw data for each pole that belong to the selected circuit or space presented in the report
weekly
For poles: 1 hour averages
For circuits/spaces: 1 hour averages for each pole that belongs to the selected circuit or space presented in the report
monthly
For poles: 1 day averages.
For circuits/spaces: 1 day averages for each pole that belongs to the selected circuit or space presented in the report
pole

Optional parameter
Can be specified only once
Number is expected
Data for specified pole is presented in report
If pole is specified, the circuit must be specified too
Checked for an exact match
circuit

Optional parameter
Can be specified only once
Must be specified if pole is specified
String format
If circuit is specified, report will contain readings for specified circuit only
If circuit and pole are specified, report will contain readings for specified pole only
If not specified, report will contain readings for all circuits
Checked for an exact match
spaceType

Optional parameter
Can be specified only once
Supported values:
suite
cage
enclosure
spaceName

Optional parameter
Can be specified only once
Text sting is expected
Report will contain circuit/poles data that is related to specified space
Checked for an exact match
readingType

Optional parameter
Can be specified only once
Supported parameters:
kW - default
amps
Specifies the type or readings should be provided in the report
format

Optional parameter
Specified as query parameter: ?format=%formattype%
Supported values:
json - default format
csv
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
siteSUW1

Example Request
Get Raw Power Data (pole level)
curl --location --request GET "{{url}}/sdp/api/power/v1/raw_data/poles?site=SUW1" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Get Raw Power Data (circuit level) 
{{url}}/sdp/api/power/v1/raw_data/circuits?site=DFW1
Retrieves actual kW and amp readings on circuit level for specified location.

Report periods:

Weekly: calculated average for every 1 hour on circuit level
Monthly: calculated average for every 1 day on circuit level
Supported filters:

site

Required parameter
Can be specified only once
Specified as query parameter: ?site=%siteName%
If no filters are specified, then the circuit metrics for the whole site will be extracted
Checked for an exact match
date

Optional parameter
Can be specified only once
Report will contains data for the specified date only
If not specified - report contains data for current date only
Date format: YYYY-MM-DD format
period

Optional parameter
Can be specified once
Supported values
weekly - default value: 1 hour averages for each selected circuit or space presented in the report
monthly: 1 day averages for each selected circuit or space presented in the report
circuit

Optional parameter
Can be specified only once
String format
If circuit is specified, report will contain readings for specified circuit only
If not specified, report will contain readings for all circuits
Checked for an exact match
spaceType

Optional parameter
Can be specified only once
Supported values:
suite
cage
enclosure
spaceName

Optional parameter
Can be specified only once
Text sting is expected
Report will contain circuit/poles data that is related to specified space
Checked for an exact match
readingType

Optional parameter
Can be specified only once
Supported parameters:
kW - default
amps
Specifies the type or readings should be provided in the report
format

Optional parameter
Specified as query parameter: ?format=%formattype%
Supported values:
json - default format
csv
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
siteDFW1

Example Request
Get Raw Power Data (circuit level)
curl --location --request GET "{{url}}/sdp/api/power/v1/raw_data/circuits?site=DFW1&date=2019-04-09&spaceType=enclosure&spaceName=ENC-DFW1-DC1-2-DH10-CL57/18" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
    "sitename": "DFW1",
    "data": [
        {
            "date": "2019-04-08",
            "datetime": "2019-04-08T00:00:00-05:00",
            "circuit": "CKT-DFW1-10M1-001-A-13,15,17-CL-57",
            "circuitType": "Primary",
            "relatedCircuit": "N/A",
            "enclosure": "ENC-DFW1-DC1-2-DH10-CL57/18",
            "parentSpace": "STE-DFW1-DC1-2-DH10-CW48",
GET Get Power Demand Report 
{{url}}/sdp/api/power/v1/power_demand?site=DFW1
Returns aggregated power details in xlsx format containing three tabs (in Postman use Send and Download):

Summary
Readings
Definitions
Report contents:

Avg kw power consumption per period
Max kW consumption per period
% Capacity Max
% Capacity Average at the following levels:
site
parent spaces
enclosure
circuit
pole
Supported filters:

site

Required parameter
Can be specified only once
Specified as query parameter: ?site=%siteName%
date

Optional parameter
Can be specified only once
Report will contains data for the specified date only
If not specified - report contains data for current date only
Date format: YYYY-MM-DD format
period

Optional parameter
Can be specified once
Supported values
daily - default value
For poles: 15 minutes raw data
For circuits/spaces: 15 minutes raw data for each pole that belongs to the selected circuit or space presented in the report
weekly
For poles: 1 hour averages
For circuits/spaces: 1 hour averages for each pole that belongs to the selected circuit or space presented in the report
monthly
For poles: 1 day averages.
For circuits/spaces: 1 day averages for each pole that belongs to the selected circuit or space presented in the report
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
siteDFW1

Example Request
Get Power Demand Report
curl --location --request GET "{{url}}/sdp/api/power/v1/power_demand?site=DFW1" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Get Raw Device Data 
{{url}}/sdp/api/power/v1/raw_device_data?site=DFW1&date=2018-10-01&period=weekly
Reports actual kW and amp readings for the specified power device dedicated to the API User environment or all power devices in specific site.

Readings of power metrics are every 15 minutes for the below supported report periods:

Daily
Weekly
Monthly
Supported filters:

site

Required parameter
Can be specified only once
Specified as query parameter: ?site=%siteName%
If no filters from the list (deviceName, deviceType) are specified, then the raw data for the whole site will be extracted
All data from power devices that belong to the specified site will be reported.
No circuit/pole data will be reported.
Checked for an exact match
date

Optional parameter
Can be specified only once
Report will contains data for the specified date only
If not specified - report contains data for current date only
Date format: YYYY-MM-DD format
period

Optional parameter
Can be specified once
Supported values
daily - default value
weekly
monthly
deviceType

Optional parameter
Can be specified once
Supported values:
UPS
PDU
PNL
deviceName

Optional parameter
Can be specified only once
Text sting is expected
Report contains device raw data
readingType

Optional parameter
Can be specified only once
supported parameters:
kW - default
amps
format

Optional parameter
Specified as query parameter: ?format=%formattype%
Supported values:
json - default format
csv
Example: /sdp/api/power/v1/raw_device_data?site=DFW1&format=csv

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
siteDFW1
date2018-10-01
periodweekly

Example Request
Get Raw Device Data
curl --location --request GET "{{url}}/sdp/api/power/v1/raw_device_data?site=DFW1&date=2019-04-01&deviceType=PDU&deviceName=PDU-DFW1-10M1-001" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "sitename": "DFW1",
  "data": [
    {
      "timestamp": 1554908400000,
      "date": "2019-04-10",
      "deviceType": "PDU",
      "deviceName": "PDU-DFW1-10M1-001",
      "avgkw": 0
    },
    {
GET Get Power Report 
{{url}}/sdp/api/power/v1/power_report?site=DFW1&date=2018-09-01&period=daily
Returns aggregated power details for all power devices within your site in xlsx format (in Postman use Send and Download):

Avg kw power consumption per period
Max kW consumption per period
% Capacity Max
Current (amps) for the following:
UPS
PDU
PNL
Individual PDUs
Panels
Supported filters:

site

Required parameter
Can be specified only once
Specified as query parameter: ?site=%siteName%
date

Optional parameter
Can be specified only once
Report will contains data for the specified date only
If not specified - report contains data for current date only
Date format: YYYY-MM-DD format
period

Optional parameter
Can be specified once
Supported values
daily - default value
weekly
monthly
For daily and weekly, days from Monday to Sunday will be used in calculation
For monthly, days from 1 to last day of month will be used in calculation
For example: Today's date is October 30, period=monthly, report will contains records from October 1 - 31
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
siteDFW1
date2018-09-01
perioddaily

Example Request
Get Power Report
curl --location --request GET "{{url}}/sdp/api/power/v1/power_report?site=DFW1&date=2018-09-01&period=daily" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Get Power Overage Daily Report (circuit pair) 
{{url}}/sdp/api/power/v1/power_overage_daily?site=DFW1&date=2018-09-01&period=daily
Provides power overage information on circuit pair level observed during specified period. Circuit pair is included in the report in case it breaks the rule: sum of peak load exceeds Power Analytics#AmpsBasedPowerCapacityThreshold (low value of 80%) of the capacity of the primary circuit.

Supported parameters:

site

required query parameter, specified as ?site=%sitename%
report contains records for specified site only
date

optional parameter
if not defined, defaults to current date
YYYY-MM-DD format is expected
can be specified once
period

optional parameter
supported values: daily, weekly, monthly
if not defined, daily is default
for weekly period - days from Mon to Sun would be used in calculation
for monthly period - days from 1 to 30(31) day of the specified month would be used in calculation (ex: date=30 Nov, period=monthly, report contains records for period November 1-31)
can be specified once
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
siteDFW1
date2018-09-01
perioddaily

Example Request
Get Power Overage Daily Report (circuit pair)
curl --location --request GET "{{url}}/sdp/api/power/v1/power_overage_daily?site=DFW1&date=2018-09-01&period=daily" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "cpy": "CPY000000005589",
    "site": "DFW1",
    "enclosureName": "ENC-DFW1-DC1-2-DH5-DY125/1",
    "primaryCircuitName": "CKT-DFW1-5D2-001-A-31,33,35-DY-125",
    "redundantCircuitName": "CKT-DFW1-5E1-001-A-31,33,35-DY-125",
    "primaryAmpsRating": 30,
    "redundantAmpsRating": 30,
    "redundantMaxAmps": 12.1357,
    "redundantAvgAmps": 7.482018,
Asset Manager

GET Get Equipment Data Dictionary
{{url}}/assetmanager/api/assetmanager/v1/dictionaries
Get list of field names and values to be used in API requests for Asset Manager items.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get Equipment Data Dictionary
curl --location --request GET "{{url}}/assetmanager/api/assetmanager/v1/dictionaries" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "applicationTypes": [
    {
      "key": "5",
      "value": "Antivirus"
    },
    {
      "key": "1",
      "value": "Apache HTTP"
    },
    {
POST Equipment Search 
{{url}}/assetmanager/api/inventoryequipment/basicsearch
Returns list of equipment records. By default method returns all equipment records for your environments and locations.

Location and environment must be specified in body.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
 "environmentName": "demo_environment",
 "type": "Server",
 "status": null,
 "serial": null,
 "customerHostName": null,
 "manufacturer": null,
 "model": null,
 "location": "DFW1",
 "selectNestedLocations": true
}


Example Request
Equipment Search
curl --location --request POST "{{url}}/assetmanager/api/inventoryequipment/basicsearch" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
 \"location\": \"DFW1\"

}"
Example Response
200 － OK
{
  "items": [
    {
      "id": 1342309,
      "version": 1,
      "backupSystem": null,
      "cpu": "",
      "customerAssetTag": null,
      "depth": "full",
      "drives": [],
      "environmentName": "qts-sales-sam-comp",
POST Manparts Search 
{{url}}/assetmanager/api/inventoryequipment/searchmanparts
Returns list of manufacturer parts to be used for new physical device creation. For use on non-QTS managed environments only.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"partNumber":"",
"manufacturer":"",
"equipmentType":"",
"model":"",
"isCustomer":false
}


Example Request
Manparts Search
curl --location --request POST "{{url}}/assetmanager/api/inventoryequipment/searchmanparts" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"partNumber\":\"\",
\"manufacturer\":\"\",
\"equipmentType\":\"\",
\"model\":\"\",
\"isCustomer\":false
}"
Example Response
200 － OK
{
    "items": [
        {
            "id": 1014,
            "partNumber": "3PAR 7200 controller",
            "manufacturer": "3PAR",
            "equipmentType": "Storage",
            "model": "3PAR 7200 controller",
            "rackUnits": 1,
            "deviceDepth": "full",
            "deviceWidth": "full",
POST Create New Physical Equipment 
{{url}}/assetmanager/api/inventoryequipment/equipments/physical
Create new physical equipment record.

Required parameters:

status
Available
Decommissioning
Leased
Production
Provisioning
RMA
Spare
Stock
Test/Training
quantity
numeric, max 3 symbols allowed
default is 1
creates as many devices, as it is stated
environmentName
powerSupplies
text Integer 100 max
manufacturerPartId
Use /assetmanager/api/inventoryequipment/searchmanparts to find manufacturerPartID (only for non-QTS manageged environments)

Optional parameters:

startingNumber in case 'quantity' is 1
serial, text max length 60
customerAssetTag, text max length 40
customerHostname
must be unique across equipment of same environment
lowercase
only alpha-numeric characters, underscores and hyphens
Max length is 50 characters
Validation message: "Host names must be all lowercase, and can only contain alpha-numeric characters, underscores and hyphens"
Symbol # should be specified to set hostname counter but only in case the hostname is not empty
comments, text max 255 characters
racking
id, text identifier for the location
elevation numeric number of elevation
deviceAlignment (ignored if placed in non-rack container)
front
rear
racked
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
 "status": "Provisioning",
 "quantity": 1,
 "startingNumber": 1,
 "environmentName": "qts-demo",
 "powerSupplies": 3,
 "serial": "api_test",
 "customerAssetTag": "api_test_1",
 "customerHostName": "api_test_1",
 "comments": "w54",
 "manufacturerPartId": {
 "value": 78
 }
}


Example Request
Create New Physical Equipment
curl --location --request POST "{{url}}/assetmanager/api/inventoryequipment/equipments/physical" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
 \"status\": \"Provisioning\",
 \"quantity\": 1,
 \"startingNumber\": 1,
 \"environmentName\": \"qts-sales-sam-smartrack\",
 \"powerSupplies\": 3,
 \"serial\": \"api_test\",
 \"customerAssetTag\": \"api_test_1\",
 \"customerHostName\": \"api_test_1\",
 \"comments\": \"w54\",
 \"manufacturerPartId\": {
 \"value\": 78
 }
}"
Example Response
200 － OK
[
  1345273
]
POST Create New VM Equipment 
{{url}}/assetmanager/api/inventoryequipment/equipments/vm
Creates new Virtual Machine (VM) device.

Supported parameters must be specified in body.

Use /assetmanager/api/assetmanager/v1/dictionaries to identify key values.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
  "environmentName": "demo_environment",
  "ram": 10,
  "ramSizeUnit": "GB",
  "cpuQuantity": 3,
  "totalStorage": 10,
  "totalStorageSizeUnit": "GB",
  "customerHostName": "test_vm_postman_1",
  "os": "FreeBSD",
  "osVer": "1",
  "fileSystems": [
    {
      "name": "first filesystem",
      "size": 10,
      "sizeUnit": "GB",
      "type": {
        "key": "1",
        "value": "NTFS"
      },
      "blockSize": 4096
    },
    {
      "name": "second filesystem",
      "size": 11,
      "sizeUnit": "GB",
      "type": {
        "key": "7",
        "value": "swap"
      },
      "blockSize": 8192
    }
  ],
  "applications": [
    {
      "type": {
        "key": "5",
        "value": "Antivirus"
      },
      "comment": "test comment"
    },
    {
      "type": {
        "key": "4",
        "value": "Database"
      },
      "comment": null
    }
  ],
  "functions": [
    "3",
    "1",
    "5",
    "4",
    "6",
    "2"
  ],
  "notes": "test notes",
  "status": "Available",
  "quantity": 1,
  "startingNumber": 1
}


Example Request
Create New VM Equipment
curl --location --request POST "{{url}}/assetmanager/api/inventoryequipment/equipments/vm" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
  \"environmentName\": \"demo_environment\",
  \"ram\": 10,
  \"ramSizeUnit\": \"GB\",
  \"cpuQuantity\": 3,
  \"totalStorage\": 10,
  \"totalStorageSizeUnit\": \"GB\",
  \"customerHostName\": \"test_vm_postman_1\",
  \"os\": \"FreeBSD\",
  \"osVer\": \"1\",
  \"fileSystems\": [
    {
      \"name\": \"first filesystem\",
      \"size\": 10,
      \"sizeUnit\": \"GB\",
      \"type\": {
        \"key\": \"1\",
        \"value\": \"NTFS\"
      },
      \"blockSize\": 4096
    },
    {
      \"name\": \"second filesystem\",
      \"size\": 11,
      \"sizeUnit\": \"GB\",
      \"type\": {
        \"key\": \"7\",
        \"value\": \"swap\"
      },
      \"blockSize\": 8192
    }
  ],
  \"applications\": [
    {
      \"type\": {
        \"key\": \"5\",
        \"value\": \"Antivirus\"
      },
      \"comment\": \"test comment\"
    },
    {
      \"type\": {
        \"key\": \"4\",
        \"value\": \"Database\"
      },
      \"comment\": null
    }
  ],
  \"functions\": [
    \"3\",
    \"1\",
    \"5\",
    \"4\",
    \"6\",
    \"2\"
  ],
  \"notes\": \"test notes\",
  \"status\": \"Available\",
  \"quantity\": 1,
  \"startingNumber\": 1
}"
PUT Update VM Equipment 
{{url}}/assetmanager/api/inventoryequipment/equipments/vm/754568
Edits virtual machine properties.

Full equipment properties must be specified in the request body. EquipmentID is required.

Supported parameters:

customerHostname
Must be unique (server validation with dialog) across equipment of same environment
Lowercase, only alpha-numeric characters, underscores and hyphens
Maximum character length is 50
Symbol # should be specified to set hostname counter when hostname is not empty
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
  "id": 754568,
  "backupSystem": "NetBackup",
  "environmentName": "kim_demo_environment",
  "status": "Available",
  "customerHostName": "test_vm_portman_2",
  "os": "FreeBSD",
  "osVer": "12",
  "cpu": {
    "id": 33041,
    "type": {
      "key": "1",
      "value": "Virtual CPU"
    },
    "quantity": 3
  },
  "ram": 1,
  "ramSizeUnit": "TB",
  "serviceLevel": null,
  "serviceOrderRenewalTerm": null,
  "serviceOrderRenewalType": null,
  "serviceOrderNumber": "123_test_service_order",
  "version": 1,
  "totalStorage": 10,
  "totalStorageSizeUnit": "GB"
}


Example Request
Update VM Equipment
curl --location --request PUT "{{url}}/assetmanager/api/inventoryequipment/equipments/vm/754568" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
  \"id\": 754568,
  \"backupSystem\": \"NetBackup\",
  \"environmentName\": \"kim_demo_environment\",
  \"status\": \"Available\",
  \"customerHostName\": \"test_vm_portman_2\",
  \"os\": \"FreeBSD\",
  \"osVer\": \"12\",
  \"cpu\": {
    \"id\": 33041,
    \"type\": {
      \"key\": \"1\",
      \"value\": \"Virtual CPU\"
    },
    \"quantity\": 3
  },
  \"ram\": 1,
  \"ramSizeUnit\": \"TB\",
  \"serviceLevel\": null,
  \"serviceOrderRenewalTerm\": null,
  \"serviceOrderRenewalType\": null,
  \"serviceOrderNumber\": \"123_test_service_order\",
  \"version\": 1,
  \"totalStorage\": 10,
  \"totalStorageSizeUnit\": \"GB\"
}"
PUT Update Physical Equipment 
{{url}}/assetmanager/api/inventoryequipment/equipments/physical/754569
Updates new physical equipment.

All equipment properties must be specified in the request body. EquipmentID is required.

Supported parameters:

customerHostname
Must be unique (server validation with dialog) across equipment of same environment
Lowercase, only alpha-numeric characters, underscores and hyphens
Maximum character length is 50
Symbol # should be specified to set hostname counter when hostname is not empty
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"id":754569,
"assetTag":null,
"auditDate":null,
"authType":null,
"backupSystem":null,
"comments":"serial12345",
"complianceType":null,
"em7DeviceId":null,
"environmentName":"kim_demo_environment",
"status":"Available",
"hostName":null,
"customerHostName":"serial12345678",
"domainName":null,
"os":null,
"osVer":null,
"platformInternal":null,
"cpu":null,"ram":null,
"ramSizeUnit":"GB",
"serviceLevel":null,
"maintBeginDate":null,
"maintEndDate":null,
"maintContractNumber":null,
"masterMaintContractNumber":null,
"serviceOrderRenewalTerm":null,
"serviceOrderRenewalType":null,
"serviceOrderExpirationDate":null,"serviceOrderNumber":null,
"maintSupportLevel":null,
"version":1,"awol":null,
"customerAssetTag":"serial12345678",
"dateReceived":null,
"leaseBeginDate":null,
"leaseEndDate":null,
"leaseNumber":null,
"leasingCompany":null,
"poNumber":null,
"powerDraw":null,
"powerSupplies":4,
"drives":[],
"vendor":null,
"console":null,
"supportAgreement":null,
"disposalDate":null,
"manufacturerPartId":{"value":1182}}


Example Request
Update Physical Equipment
curl --location --request PUT "{{url}}/assetmanager/api/inventoryequipment/equipments/physical/754569" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"id\":754569,
\"assetTag\":null,
\"auditDate\":null,
\"authType\":null,
\"backupSystem\":null,
\"comments\":\"serial12345\",
\"complianceType\":null,
\"em7DeviceId\":null,
\"environmentName\":\"kim_demo_environment\",
\"status\":\"Available\",
\"hostName\":null,
\"customerHostName\":\"serial12345678\",
\"domainName\":null,
\"os\":null,
\"osVer\":null,
\"platformInternal\":null,
\"cpu\":null,\"ram\":null,
\"ramSizeUnit\":\"GB\",
\"serviceLevel\":null,
\"maintBeginDate\":null,
\"maintEndDate\":null,
\"maintContractNumber\":null,
\"masterMaintContractNumber\":null,
\"serviceOrderRenewalTerm\":null,
\"serviceOrderRenewalType\":null,
\"serviceOrderExpirationDate\":null,\"serviceOrderNumber\":null,
\"maintSupportLevel\":null,
\"version\":1,\"awol\":null,
\"customerAssetTag\":\"serial12345678\",
\"dateReceived\":null,
\"leaseBeginDate\":null,
\"leaseEndDate\":null,
\"leaseNumber\":null,
\"leasingCompany\":null,
\"poNumber\":null,
\"powerDraw\":null,
\"powerSupplies\":4,
\"drives\":[],
\"vendor\":null,
\"console\":null,
\"supportAgreement\":null,
\"disposalDate\":null,
\"manufacturerPartId\":{\"value\":1182}}"
PUT Update Equipment Notes 
{{url}}/assetmanager/api/inventoryequipment/equipments/37717104/buildNotes
Edits 'Notes' block for existing device.
EquipmmentID is required.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"notes":"testtest"
}


Example Request
Update Equipment Notes
curl --location --request PUT "{{url}}/assetmanager/api/inventoryequipment/equipments/37717104/buildNotes" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"notes\":\"testtest\"
}"
DEL Delete Equipment 
{{url}}/assetmanager/api/inventoryequipment/equipments?ids=754569
Deletes equipment for non-QTS managed environments only.

Several equipment records can be deleted at a time by specifying multiple IDs in the request URL:
/assetmanager/api/inventoryequipment/equipments?ids=754052,754056

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
PARAMS
ids754569

Example Request
Delete Equipment
curl --location --request DELETE "{{url}}/assetmanager/api/inventoryequipment/equipments?ids=754569" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data ""
Online Ordering

This folder contains APIs for the following ordering functions:

crossConnect
Switchboard
Swtichboard Port Order
Switchboard Virtual Connect Order
Remote Hands
Online Ordering General

These APIs are common to online ordering APIs.

GET Get List of Contracts 
{{url}}/sdp/ordering/v1/api/contracts
Returns list of contracts. Please note salesForceContractId must be specified for new orders and contract location should match the specified order location.

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get list of Contracts
curl --location --request GET "{{url}}/sdp/ordering/v1/api/contracts" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "salesForceContractId": "800f2000001fnVdAAI",
    "number": "00007127",
    "gpNumber": "Test.12346.Test",
    "location": "CHI1",
    "startDate": "05-01-2018",
    "endDate": "04-30-2019"
  },
  {
    "salesForceContractId": "8002F0000003oSIQAY",
GET Get List of Carrier Hotel Locations
{{url}}/sdp/ordering/v1/api/sp/carrier_hotel_locations
Returns list of supported Carrier Hotel Locations available for Swithboard port order.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get List of Carrier Hotel Locations
curl --location --request GET "{{url}}/sdp/ordering/v1/api/sp/carrier_hotel_locations" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "id": 2,
    "shortAddress": "111 8th Ave"
  },
  {
    "id": 7,
    "shortAddress": "56 Marietta"
  },
  {
    "id": 5,
GET Get List of Contracts for a Specific Carrier Hotel
{{url}}/sdp/ordering/v1/api/sp/contracts?carrier_hotel_location=<carrier hotel id>
Lists contracts that can be used to order Switchboard port to a specific Carrier Hotel location.

Carrier Hotel ID must be specified as url parameter.

Carrier Hotel ID can be obtained using:
/sdp/ordering/v1/api/sp/carrier_hotel_locations

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
carrier_hotel_location<carrier hotel id>

Example Request
Get List of Contracts for a Specific Carrier Hotel
curl --location --request GET "{{url}}/sdp/ordering/v1/api/sp/contracts?carrier_hotel_location=5" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "salesForceContractId": "800f2000001fnVdAAI",
    "number": "00007127",
    "gpNumber": "Test.12346.Test",
    "location": "CHI1",
    "startDate": "05-01-2018",
    "endDate": "04-30-2019"
  }
]
crossConnect

GET Get List of crossConnect Orders 
{{url}}/sdp/ordering/v1/api/ccx/orders
Returns list of existing crossConnect orders.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get crossConnect order list
curl --location --request GET "{{url}}/sdp/ordering/v1/api/ccx/orders" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "pageInfo": {
    "pageSize": 20,
    "totalCount": 26,
    "totalPages": 2,
    "pageNumber": 1
  },
  "data": [
    {
      "id": 205,
      "number": "CCX-000000069",
GET Get List of Data Centers 
{{url}}/sdp/ordering/v1/api/ccx/locations
Returns list of your locations (data centers) where crossConnect is available.

Response parameter "name" is the data center site ID/location required to submit crossConnect orders.

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get list of supported Data Centers for CCX services
curl --location --request GET "{{url}}/sdp/ordering/v1/api/ccx/locations" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "id": 12639,
    "name": "IAD1",
    "city": null,
    "state": null
  },
  {
    "id": 66483,
    "name": "CHI1",
    "city": null,
GET Get List of Racks 
{{url}}/sdp/ordering/v1/api/sp/racks?location=MIA1
Returns list of racks that can be used for crossConnect orders.

Required parameter:

location: data center site ID
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
locationMIA1

Example Request
Get list of racks
curl --location --request GET "{{url}}/sdp/ordering/v1/api/ccx/racks/?location=CHI1" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "rackId": "327da219dba213080caef3a31d96195b",
    "name": "ENC-CHI1-DC1-1-DH1-?-?",
    "locationName": "CHI1",
    "parentSpaceName": "DC1",
    "buildingName": "DC1",
    "spaceStatus": "Reserved"
  },
  {
    "rackId": "693e6a99dba213080caef3a31d9619aa",
GET crossConnect Data Dictionary
{{url}}/sdp/ordering/v1/api/ccx/circuit_connectivity_information
Returns available values and ids for cable types and connection types that can be used to create or update crossConnect orders.

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
crossConnect dictionary data
curl --location --request GET "{{url}}/sdp/ordering/v1/api/ccx/circuit_connectivity_information" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "crossConnectTypeId": "GIG_E_1",
    "crossConnectTypeName": "1 Gig-E",
    "cableTypes": [
      {
        "id": "COPPER_CAT6",
        "name": "Copper-cat6",
        "connectors": [
          {
            "id": "RJ45",
GET Get crossConnect Order Price
{{url}}/ccx/orders/{orderID}/get_price
Returns monthly price and set up charge for existing crossConnect order. Price is estimated price when order has not been approved by QTS.

orderID is required parameter.

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get CrossConnect order price
curl --location --request GET "{{url}}/ccx/orders/%7BOrderID%7D/get_price" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data ""
Example Response
200 － OK
{
	"monthlyPrice"; 150,
	"setupCharge"; 1500
}
POST Create Draft 'Connect to My Cage' crossConnect Order 
{{url}}/sdp/ordering/v1/api/ccx/orders/
Creates new 'Connect to My Cage' draft crossConnect order. This endpoint consumes multipart/form-data which consists of JSON part and attachment part. It may be helpful to use the SDP UI as a guide when developing your first API request. A Letter of Authorization (LOA) is not required for a 'Connect to My Cage' crossConnect order.

Required parameters:

location id (data center)

destinationType (CONNECT_TO_MY_CAGE)

ccxTypeId (GIG_E_1, GIG_E_10, GIG_E_25, GIG_E_50, GIG_E_100, DARK_FIBER, POTS, DS1)

cableTypeId (COPPER_CAT6, MMF_OM1, MMF_OM3, SMF)

salesforceContract id

sideA

rackId
endpointType (PATCH_PANEL, TERMINATE_TO_RACK)
connectorTypeId (FC, LC, SC)
panelShelf (if endpointType is PATCH_PANEL)
patchPanel (if endpointType is PATCH_PANEL)
portBlock (if endpointType is PATCH_PANEL)
sideZ

rackId
endpointType (PATCH_PANEL, TERMINATE_TO_RACK)
connectorTypeId (FC, LC, SC)
panelShelf (if endpointType is PATCH_PANEL)
patchPanel (if endpointType is PATCH_PANEL)
portBlock (if endpointType is PATCH_PANEL)
Optional parameters:

dataCircuitName
customerReferenceName
expeditedOrder
comment
rackName
port
GENERIC_ATTACHMENT (file/attachment)
Multipart Request:

Body content disposition must be form-data
Filename must be "order" content type: application/JSON
Attachment
Attachment part has name "MY_CAGE_LETTER_OF_AUTHORIZATION", "GENERIC_ATTACHMENT"
Maximum file size is 6 MB
Content type must match document type (or set to auto)
Allowed document types:
pdf (content type: application/pdf)
txt
doc, docx
xls, xlsx
The required parameters can be obtained using the followihg requests:

/sdp/ordering/v1/api/contracts
/sdp/ordering/v1/api/ccx/racks/?location={location}
/sdp/ordering/v1/api/ccx/circuit_connectivity_information
Submit Draft crossConnect orders using: POST /sdp/ordering/v1/api/ccx/orders/{orderid}/submit

Example request:

{

"cableTypeDescription":"Copper-cat6", "cableTypeId":"COPPER_CAT6", "ccxTypeDescription":"1 Gig-E", "ccxTypeId":"GIG_E_1", "customerReferenceName":"testib070504", "dataCircuitName":"testib070504", "destinationType":"CONNECT_TO_MY_CAGE", "expeditedOrder":false, "requestedDueDate":"2019-04-16",

"location":{"id": 66483, "name": "CHI1"},

"sideA":{ "connectorTypeId": "RJ45", "destinationType": "CONNECT_TO_MY_CAGE", "endpointType":"PATCH_PANEL", "panelShelf": "1", "patchPanel":"1", "port":"1", "portBlock": "1", "rackId" : "327da219dba213080caef3a31d96195b"

},

"sideZ":{ "comment":"2", "connectorTypeId":"RJ45", "destinationType":"CONNECT_TO_MY_CAGE", "endpointType":"PATCH_PANEL", "panelShelf":"2", "patchPanel":"2", "port":"2", "portBlock":"2", "rackId":"693e6a99dba213080caef3a31d9619aa"

},

"salesforceContract":{ "location":"CHI1", "salesForceContractId":"800f2000001fnVdAAI"

}

}

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
order
MY_CAGE_LETTER_OF_AUTHORIZATION
GENERIC_ATTACHMENT

Example Request
Create Draft 'Connect to My Cage' crossConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/ccx/orders/" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{

\"cableTypeDescription\":\"Copper-cat6\",
\"cableTypeId\":\"COPPER_CAT6\",
\"ccxTypeDescription\":\"1 Gig-E\",
\"ccxTypeId\":\"GIG_E_1\",
\"customerReferenceName\":\"testib070504\",
\"dataCircuitName\":\"testib070504\",
\"destinationType\":\"CONNECT_TO_MY_CAGE\",
\"expeditedOrder\":false,
\"requestedDueDate\":\"2019-04-16\",

\"location\":{\"id\": 66483, \"name\": \"CHI1\"},

\"sideA\":{
	\"connectorTypeId\": \"RJ45\", 
	\"destinationType\": \"CONNECT_TO_MY_CAGE\", 
	\"endpointType\":\"PATCH_PANEL\", 
	\"panelShelf\": \"1\", 
	\"patchPanel\":\"1\", 
	\"port\":\"1\",
	\"portBlock\": \"1\",
	\"rackId\" : \"327da219dba213080caef3a31d96195b\"
	
},

\"sideZ\":{
	\"comment\":\"2\",
	\"connectorTypeId\":\"RJ45\",
	\"destinationType\":\"CONNECT_TO_MY_CAGE\",
	\"endpointType\":\"PATCH_PANEL\",
	\"panelShelf\":\"2\",
	\"patchPanel\":\"2\", 
	\"port\":\"2\",
	\"portBlock\":\"2\",
	\"rackId\":\"693e6a99dba213080caef3a31d9619aa\"

},

\"salesforceContract\":{
	
	\"location\":\"CHI1\",
	
	\"salesForceContractId\":\"800f2000001fnVdAAI\"
	
}

}"
Example Response
201 － Created
{
  "id": 609,
  "number": "CCX-000000133",
  "created": "2019-04-16T19:52:09.136 +0000",
  "location": {
    "id": 66483,
    "name": "CHI1",
    "city": "Chicago",
    "state": "IL"
  },
  "createdByEmployee": false,
POST Create Draft 'Connect to Carrier' crossConnect Order 
{{url}}/sdp/ordering/v1/api/ccx/orders/
Creates new 'Connect to Carrier' draft crossConnect order. This endpoint consumes multipart/form-data which consists of JSON part and attachment part. It may be helpful to use the SDP UI as a guide when developing your first API request. CARRIER_LETTER_OF_AUTHORIZATION is required.

Required parameters:

location id (data center)

destinationType (CONNECT_TO_CARRIER)

ccxTypeId (GIG_E_1, GIG_E_10, GIG_E_25, GIG_E_50, GIG_E_100, DARK_FIBER, POTS, DS1)

cableTypeId (COPPER_CAT6, MMF_OM1, MMF_OM3, SMF)

salesforceContract id

sideA

CARRIER_LETTER_OF_AUTHORIZATION
sideZ

rackId
endpointType (PATCH_PANEL, TERMINATE_TO_RACK)
panelShelf (if endpointType is PATCH_PANEL)
patchPanel (if endpointType is PATCH_PANEL)
portBlock (if endpointType is PATCH_PANEL)
Optional parameters:

dataCircuitName
customerReferenceName
expeditedOrder
comment
rackName
port
CARRIER_FACILITY_AGREEMENT (file/attachment)
GENERIC_ATTACHMENT (file/attachment)
Multipart Request:

Body content disposition must be form-data
Filename must be "order" content type: application/JSON
Attachment
Attachment part has name "CARRIRER_LETTER_OF_AUTHORIZATION", "CARRIER_FACILITY_AGREEMENT", "GENERIC_ATTACHMENT"
Maximum file size is 6 MB
Content type must match document type (or set to auto)
Allowed document types:
pdf (content type: application/pdf)
txt
doc, docx
xls, xlsx
The required parameters can be obtained using the followihg requests:

/sdp/ordering/v1/api/contracts
/sdp/ordering/v1/api/ccx/racks/?location={location}
/sdp/ordering/v1/api/ccx/circuit_connectivity_information

Submit Draft crossConnect orders using: POST /sdp/ordering/v1/api/ccx/orders/{orderid}/submit

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY


Example Request
Create Draft 'Connect to Carrier' crossConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/ccx/orders/" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{

\"cableTypeDescription\":\"Copper-cat6\",
\"cableTypeId\":\"COPPER_CAT6\",
\"ccxTypeDescription\":\"1 Gig-E\",
\"ccxTypeId\":\"GIG_E_1\",
\"customerReferenceName\":\"testib070504\",
\"dataCircuitName\":\"testib070504\",
\"destinationType\":\"CONNECT_TO_MY_CAGE\",
\"expeditedOrder\":false,
\"requestedDueDate\":\"2019-04-16\",

\"location\":{\"id\": 66483, \"name\": \"CHI1\"},

\"sideA\":{
	\"connectorTypeId\": \"RJ45\", 
	\"destinationType\": \"CONNECT_TO_MY_CAGE\", 
	\"endpointType\":\"PATCH_PANEL\", 
	\"panelShelf\": \"1\", 
	\"patchPanel\":\"1\", 
	\"port\":\"1\",
	\"portBlock\": \"1\",
	\"rackId\" : \"327da219dba213080caef3a31d96195b\"
	
},

\"sideZ\":{
	\"comment\":\"2\",
	\"connectorTypeId\":\"RJ45\",
	\"destinationType\":\"CONNECT_TO_MY_CAGE\",
	\"endpointType\":\"PATCH_PANEL\",
	\"panelShelf\":\"2\",
	\"patchPanel\":\"2\", 
	\"port\":\"2\",
	\"portBlock\":\"2\",
	\"rackId\":\"693e6a99dba213080caef3a31d9619aa\"

},

\"salesforceContract\":{
	
	\"location\":\"CHI1\",
	
	\"salesForceContractId\":\"800f2000001fnVdAAI\"
	
}

}"
Example Response
201 － Created
{
  "id": 610,
  "number": "CCX-000000134",
  "created": "2019-04-16T20:09:50.651 +0000",
  "location": {
    "id": 66483,
    "name": "CHI1",
    "city": "Chicago",
    "state": "IL"
  },
  "createdByEmployee": false,
POST Submit crossConnect Order 
{{url}}/sdp/ordering/v1/api/ccx/orders/609/submit
Submits a draft order.

Required parameter:

orderid
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY


Example Request
Submit crossConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/ccx/orders/609/submit" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json"
POST Resubmit crossConnect Order 
{{url}}/sdp/ordering/v1/api/ccx/orders/resubmit
Resubmit your crossConnect order that is currently in 'Need More Information' status so DCO can get updated details of the order and your comments.

This endpoint consumes multipart/form-data which consists of JSON part and attachment part.

JSON name = "order"

location id
ccxTypeId
GIG_E_1
GIG_E_10
ccxTypeDescription
cableTypeId: SMF
cableTypeDescription
expeditedOrder: true, false
sideA destinationType: CONNECT_TO_MY_CAGE, PATCH_PANEL
connectorTypeId: LC
rackId
rackName
endpointType: PATCH_PANEL
sideZ destinationType: CONNECT_TO_MY_CAGE,
connectorTypeId: LC
endpointType: PATCH_PANEL
salesForceContractId
destinationType: CONNECT_TO_MY_CAGE, CONNECT_TO_CARRIER
Attachment

Attachment part has name "CARRIER_LETTER_OF_AUTHORIZATION"
Maximum file size is 6 MB
Allowed document types:
pdf
txt
doc, docx
xls, xlsx
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
order

Example Request
Resubmit crossConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/ccx/orders/resubmit" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --form "order=@"
POST Cancel crossConnect Order 
{{url}}/sdp/ordering/v1/api/ccx/orders/cancel
Cancels existing crossConnect order.

Required parameters:

OrderId
CancelUserComment
Only crossConnect orders with 'Awaiting Internal Review' and 'Need More Information' statuses can be cancelled.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"orderId":10449,
"cancelUserComment":"Cancelled VP order as part of acceptance process"
}


Example Request
Cancel crossConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/ccx/orders/cancel" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"orderId\":10449,
\"cancelUserComment\":\"Cancelled VP order as part of acceptance process\"
}"
PUT Update crossConnect Order 
{{url}}/sdp/ordering/v1/api/ccx/orders/10449
Update a crossConnect order that is not yet submitted.

Full order details must be specified in the request body.

Order ID must be specified in the URL.

This endpoint consumes multipart/form-data which consists of JSON part and attachment part.

JSON part has name "order"

location id
ccxTypeId
GIG_E_1
GIG_E_10
ccxTypeDescription
cableTypeId: SMF
cableTypeDescription
expeditedOrder: true, false
sideA destinationType: CONNECT_TO_MY_CAGE, PATCH_PANEL
connectorTypeId: LC
rackId
rackName
endpointType: PATCH_PANEL
sideZ destinationType: CONNECT_TO_MY_CAGE,
connectorTypeId: LC
endpointType: PATCH_PANEL
salesForceContractId
destinationType: CONNECT_TO_MY_CAGE, CONNECT_TO_CARRIER
Allowed attachment files:

CARRIER_LETTER_OF_AUTHORIZATION (for 'Connect to Carrier' type)
CARRIER_FACILITY_AGREEMENT (for 'Connect to Carrier' type)
MY_CAGE_LETTER_OF_AUTHORIZATION (for 'Connect to My cage type')
GENERIC_ATTACHMENT
Allowed attachment document types:

pdf
txt
doc, docx
xls, xlsx
Maximum attachment file size is 6 MB

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typemultipart/form-data
BODY
order{ "location": { "id": 3289737 }, "ccxTypeId": "GIG_E_1", "ccxTypeDescription": "1 Gig-E", "cableTypeId": "SMF", "cableTypeDescription": "SMF", "expeditedOrder": false, "sideA": { "destinationType": "CONNECT_TO_MY_CAGE", "connectorTypeId": "LC", "rackId": "84b0b8596fe215400247af512e3ee47c", "rackName": "ENC-MIA1-DC1-1-POD2-208-05-Rack QVI03", "endpointType": "PATCH_PANEL" }, "sideZ": { "destinationType": "CONNECT_TO_MY_CAGE", "connectorTypeId": "LC", "endpointType": "PATCH_PANEL" }, "salesforceContract": { "salesForceContractId": "800q0000000MRgyAAG" }, "destinationType": "CONNECT_TO_MY_CAGE" }
LETTER_OF_AUTHORIZATION

Example Request
Update crossConnect Order
curl --location --request PUT "{{url}}/sdp/ordering/v1/api/ccx/orders/10449" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: multipart/form-data" \
  --form "order={
    \"location\": {
        \"id\": 3289737
    },
    \"ccxTypeId\": \"GIG_E_1\",
    \"ccxTypeDescription\": \"1 Gig-E\",
    \"cableTypeId\": \"SMF\",
    \"cableTypeDescription\": \"SMF\",
    \"expeditedOrder\": false,
    \"sideA\": {
        \"destinationType\": \"CONNECT_TO_MY_CAGE\",
        \"connectorTypeId\": \"LC\",
        \"rackId\": \"84b0b8596fe215400247af512e3ee47c\",
        \"rackName\": \"ENC-MIA1-DC1-1-POD2-208-05-Rack QVI03\",
        \"endpointType\": \"PATCH_PANEL\"
    },
    \"sideZ\": {
        \"destinationType\": \"CONNECT_TO_MY_CAGE\",
        \"connectorTypeId\": \"LC\",
        \"endpointType\": \"PATCH_PANEL\"
    },
    \"salesforceContract\": {
        \"salesForceContractId\": \"800q0000000MRgyAAG\"
    },
    \"destinationType\": \"CONNECT_TO_MY_CAGE\"
    }" \
  --form "LETTER_OF_AUTHORIZATION=@"
PUT Create & Submit New crossConnect Order 
{{url}}/sdp/ordering/v1/api/ccx/orders/submit
Create and submit new crossConnect order.

This endpoint consumes multipart/form-data which consists of JSON part and attachment part.

JSON part has name "order"

Allowed attachment types:

CARRIER_LETTER_OF_AUTHORIZATION (for 'Connect to Carrier' type)
CARRIER_FACILITY_AGREEMENT (for 'Connect to Carrier' type)
MY_CAGE_LETTER_OF_AUTHORIZATION (for 'Connect to My Cage type')
GENERIC_ATTACHMENT
Allowed attachment document types:

pdf
txt
doc, docx
xls, xlsx
Maximum attachment file size is 6 MB

HEADERS
AuthorizationBearer 72ffff0c-fa16-405d-8168-4dd3f9054960
Content-Typeapplication/x-www-form-urlencoded
BODY
{"id":null,"location":{"id":3289815,"name":"CHI1","city":null,"state":null},"ccxTypeId":"GIG_E_25","cableTypeId":"MMF_OM3","expeditedOrder":false,"customerReferenceName":"Customer Reference Name","salesforceContract":{"salesForceContractId":"800q0000000MwM0AAK","number":"00006414","gpNumber":"gp_for CHI1","location":"CHI1","startDate":"08-01-2018","endDate":"12-31-2018"},"sideA":{"destinationType":"CONNECT_TO_CARRIER","carrierName":"Carrier","carrierOrderNumber":"Carrier Order Number","carrierFocDate":"2018-12-07","carrierCircuitId":null,"localLoopProvider":null,"lecCircuitId":null,"connectorTypeId":"FC","panelShelf":null,"portBlock":null},"sideZ":{"destinationType":"CONNECT_TO_MY_CAGE","rackId":"8b33a348db124300a571ff851d9619e6","rackName":"ENC-CHI1-DC1-1-DH1-KG16","endpointType":"PATCH_PANEL","patchPanel":"Patch Panel name","port":null,"comment":null,"connectorTypeId":"LC","panelShelf":"test panel/shelf","portBlock":"Port block"},"dataCircuitName":"Circuit Name","destinationType":"CONNECT_TO_CARRIER"}


Example Request
Create & Submit New crossConnect Order
curl --location --request PUT "{{url}}/sdp/ordering/v1/api/ccx/orders/submit" \
  --header "Authorization: Bearer 72ffff0c-fa16-405d-8168-4dd3f9054960" \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --data "{\"id\":null,\"location\":{\"id\":3289815,\"name\":\"CHI1\",\"city\":null,\"state\":null},\"ccxTypeId\":\"GIG_E_25\",\"cableTypeId\":\"MMF_OM3\",\"expeditedOrder\":false,\"customerReferenceName\":\"Customer Reference Name\",\"salesforceContract\":{\"salesForceContractId\":\"800q0000000MwM0AAK\",\"number\":\"00006414\",\"gpNumber\":\"gp_for CHI1\",\"location\":\"CHI1\",\"startDate\":\"08-01-2018\",\"endDate\":\"12-31-2018\"},\"sideA\":{\"destinationType\":\"CONNECT_TO_CARRIER\",\"carrierName\":\"Carrier\",\"carrierOrderNumber\":\"Carrier Order Number\",\"carrierFocDate\":\"2018-12-07\",\"carrierCircuitId\":null,\"localLoopProvider\":null,\"lecCircuitId\":null,\"connectorTypeId\":\"FC\",\"panelShelf\":null,\"portBlock\":null},\"sideZ\":{\"destinationType\":\"CONNECT_TO_MY_CAGE\",\"rackId\":\"8b33a348db124300a571ff851d9619e6\",\"rackName\":\"ENC-CHI1-DC1-1-DH1-KG16\",\"endpointType\":\"PATCH_PANEL\",\"patchPanel\":\"Patch Panel name\",\"port\":null,\"comment\":null,\"connectorTypeId\":\"LC\",\"panelShelf\":\"test panel/shelf\",\"portBlock\":\"Port block\"},\"dataCircuitName\":\"Circuit Name\",\"destinationType\":\"CONNECT_TO_CARRIER\"}
"
DEL Delete Draft crossConnect Order
{{url}}/sdp/ordering/v1/api/ccx/orders/<order id>
Delete draft crossConnect.

Order ID must be specified in URL.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Delete Draft crossConnect Order
curl --location --request DELETE "{{url}}/sdp/ordering/v1/api/ccx/orders/%3Corder%20id%3E" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data ""
Switchboard

Switchboard Port

GET Get List of Switchboard Port Orders
{{url}}/sdp/ordering/v1/api/sp/orders
Lists Switchboard Port orders.

Page filters are supported.

Example:

/sdp/ordering/v1/api/sp/orders?pageNumber=1&pagSize=10

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get Switchboard Port Order List
curl --location --request GET "{{url}}/sdp/ordering/v1/api/sp/orders?location=ATL1" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "pageInfo": {
    "pageSize": 20,
    "totalCount": 34,
    "totalPages": 2,
    "pageNumber": 1
  },
  "data": [
    {
      "id": 606,
      "number": "SP-000000213",
GET Get Switchboard Order Status & History 
{{url}}/sdp/ordering/v1/api/sp/orders/657/action_history
Returns status and history of applied actions for a specific Switchboard Port order.
Switchboard order ID must be specified in URL.

The Switchboard order ID can be obtained using:
/sdp/ordering/v1/api/sp/orders

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get Switchboard Order Status & History
curl --location --request GET "{{url}}/sdp/ordering/v1/api/sp/orders/602/action_history" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "orderActionHistory": [
    {
      "comment": "",
      "author": "QTS Employee",
      "operationDate": "2019-04-12T14:26:03.876+0000",
      "action": "SUBMITTED"
    },
    {
      "comment": "",
      "author": "QTS Employee",
GET Get List of Racks 
{{url}}/sdp/ordering/v1/api/sp/racks?location=MIA1
Returns list of racks that can be used for Switchboard Port orders.

Required parameter:

location: data center site ID
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
locationMIA1

Example Request
Get List of Racks
curl --location --request GET "{{url}}/sdp/ordering/v1/api/ccx/racks?location=SUW1" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "rackId": "fbb01eab6fa2d1800247af512e3ee4fd",
    "name": "ENC-SUW1-DC1-1-DHNorth-200-01",
    "locationName": "SUW1",
    "parentSpaceName": "DC1",
    "buildingName": "DC1",
    "spaceStatus": "Occupied"
  },
  {
    "rackId": "644038196fe215400247af512e3ee45e",
GET Get Switchboard Port Order Estimated Price 
{{url}}/sdp/ordering/v1/api/sp/estimate_price?orderProvider=QTS&location=MIA1&portType=GIG_E_1&expeditedOrder=false
Returns estimated price for monthly payments and setup charge based on location, port type. Expedited orders have different prices.

Example filters:

?orderProvider=QTS&location=MIA1&portType=GIG_E_1&expeditedOrder=false
?orderProvider=CARRIER_HOTEL&location=ATL1&portType=GIG_E_10&expeditedOrder=false
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
orderProviderQTS
locationMIA1
portTypeGIG_E_1
expeditedOrderfalse

Example Request
Get Switchboard Port Order Estimated Price
curl --location --request GET "{{url}}/sdp/ordering/v1/api/sp/estimate_price?orderProvider=QTS&location=MIA1&portType=GIG_E_1&expeditedOrder=false" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "monthlyPrice": 150,
  "setupCharge": 250
}
GET Get List of Data Centers 
{{url}}/sdp/ordering/v1/api/sp/locations
Returns list of locations (data centers).

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get List of Data Centers
curl --location --request GET "{{url}}/sdp/ordering/v1/api/sp/locations" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "id": 66483,
    "name": "CHI1",
    "city": null,
    "state": null
  },
  {
    "id": 66484,
    "name": "DFW1",
    "city": null,
GET Get List of Data Centers for Switchboard Port Service 
{{url}}/sdp/ordering/v1/api/sp/supported_locations
Returns list of your locations (data centers) that support Switchboard services.

Use the below API to idenfity all your locations (data centers):
/sdp/ordering/v1/api/sp/locations

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get List of Data Centers for Switchboard Port Service
curl --location --request GET "{{url}}/sdp/ordering/v1/api/sp/supported_locations" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "id": 66482,
    "name": "ATL1",
    "city": "Atlanta",
    "state": "GA"
  },
  {
    "id": 66483,
    "name": "CHI1",
    "city": "Chicago",
POST Create Draft 'Terminate to Rack' Switchboard Port Order 
{{url}}/sdp/ordering/v1/api/sp/orders
Creates new 'Terminate to Rack' Switchboard port order. It may be helpful to use the SDP UI as a guide when developing your first API request.

Required parameters for successful order submission (portProvider is the only required parameter for creation of draft order):

portProvider (QTS)
location id
salesforceContractID
expeditedOrder (true, false)
virtualPortTypeId (GIG_E_1, GIG_E_10)
cableTypeId (SMF)
exchangeVisibility (true = public; false = private)
connectorTypeId (LC, SC)
rackId
endpointType: "TERMINATE_TO_RACK"
Optional parameters for successful order submission (portProvider is the only required parameter for creation of draft order):

location name
description
customerReferenceName
comment
rackName
diverseFrom id (must be a valid Switchboard port order not in draft mode)
diverseFrom number (must be a valid Switchboard port order not in draft mode)
The required parameters can be obtained using the following requests:

/sdp/ordering/v1/api/sp/locations
/sdp/ordering/v1/api/contracts
/sdp/ordering/v1/api/sp/racks?location={datacenter site ID, ex. MIA1}
Submit Draft Switchboard orders using:

/sdp/ordering/v1/api/sp/orders/{order id}/submit
HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
  "location": {
    "id": 66482,
    "name": "ATL1"
  },
  "expeditedOrder": false,
  "physicalPort": {
    "spTypeId": "GIG_E_1",
    "cableTypeId": "SMF",
    "exchangeVisibility": true,
    "customerSide": {
      "connectorTypeId": "LC",
      "rackId": "f962d958db2f2340d655fd33399619fa",
      "endpointType": "TERMINATE_TO_RACK"
    }
  },
  "portProvider": "QTS",
  "salesforceContract": {
    "salesForceContractId": "8002F0000003oSIQAY"
  },
  "diverseFrom": {
    "id": 620,
    "number": "SP-000000226"
  }
}


Example Request
Create Draft 'Terminate to Rack' Switchboard Port Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/sp/orders" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
  \"location\": {
    \"id\": 66482,
    \"name\": \"ATL1\"
  },
  \"expeditedOrder\": false,
  \"physicalPort\": {
    \"spTypeId\": \"GIG_E_1\",
    \"cableTypeId\": \"SMF\",
    \"exchangeVisibility\": true,
    \"customerSide\": {
      \"connectorTypeId\": \"LC\",
      \"rackId\": \"f962d958db2f2340d655fd33399619fa\",
      \"endpointType\": \"TERMINATE_TO_RACK\"
    }
  },
  \"portProvider\": \"QTS\",
  \"salesforceContract\": {
    \"salesForceContractId\": \"8002F0000003oSIQAY\"
  },
  \"diverseFrom\": {
    \"id\": 620,
    \"number\": \"SP-000000226\"
  }
}"
Example Response
201 － Created
{
  "id": 621,
  "number": "SP-000000227",
  "created": "2019-04-24T17:36:46.041 +0000",
  "location": {
    "id": 66482,
    "name": "ATL1",
    "city": "Atlanta",
    "state": "GA"
  },
  "createdByEmployee": false,
POST Create Draft 'Connect to Patch Panel' Switchboard Port Order
{{url}}/sdp/ordering/v1/api/sp/orders
Creates draft 'Connect to Patch Panel' Switchboard Port order.

Required parameters for successful order submission (portProvider is the only required parameter for creation of draft order):

portProvider (QTS)
location id
salesforceContractID
expeditedOrder (true, false)
virtualPortTypeId (GIG_E_1, GIG_E_10)
cableTypeId (SMF)
exchangeVisibility (true = public; false = private)
connectorTypeId (LC, SC)
rackId
endpointType: "PATCH_PANEL"
patchPanel
port
panelShelf
portBlock
Optional parameters for successful order submission (portProvider is the only required parameter for creation of draft order):

location id name
customerReferenceName
description
diverseFrom id (must be a valid Switchboard port order not in draft mode)
diverseFrom number (must be a valid Switchboard port order not in draft mode)
comment
The required parameters can be obtained using the following requests:

/sdp/ordering/v1/api/sp/locations
/sdp/ordering/v1/api/contracts
/sdp/ordering/v1/api/sp/racks?location=MIA1
Submit Draft Switchboard orders using:

/sdp/ordering/v1/api/sp/orders/{order id}/submit
HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
  "location": {
    "id": 66492,
    "name": "SUW1"
   
  },
  "expeditedOrder": false,
  "physicalPort": {
    "spTypeId": "GIG_E_1",
    "cableTypeId": "SMF",
    "exchangeVisibility": false,
    "customerSide": {
      "connectorTypeId": "LC",
      "rackId": "fbb01eab6fa2d1800247af512e3ee4fd",
      "endpointType": "PATCH_PANEL",
      "patchPanel": "Patch Panel",
      "port": "Port Number",
      "panelShelf": "Panel/Shelf",
      "portBlock": "Port/Block"
    }
  },
  "portProvider": "QTS",
  "salesforceContract": {
    "salesForceContractId": "800f2000001gZQrAAM"
}
}


Example Request
Create Draft 'Connect to Patch Panel' Switchboard Port Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/sp/orders" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
  \"location\": {
    \"id\": 66492,
    \"name\": \"SUW1\"
   
  },
  \"expeditedOrder\": false,
  \"physicalPort\": {
    \"spTypeId\": \"GIG_E_1\",
    \"cableTypeId\": \"SMF\",
    \"exchangeVisibility\": false,
    \"customerSide\": {
      \"connectorTypeId\": \"LC\",
      \"rackId\": \"fbb01eab6fa2d1800247af512e3ee4fd\",
      \"endpointType\": \"PATCH_PANEL\",
      \"patchPanel\": \"Patch Panel\",
      \"port\": \"Port Number\",
      \"panelShelf\": \"Panel/Shelf\",
      \"portBlock\": \"Port/Block\"
    }
  },
  \"portProvider\": \"QTS\",
  \"salesforceContract\": {
    \"salesForceContractId\": \"800f2000001gZQrAAM\"
}
}"
Example Response
201 － Created
{
  "id": 628,
  "number": "SP-000000232",
  "created": "2019-04-25T20:40:25.438 +0000",
  "location": {
    "id": 66492,
    "name": "SUW1",
    "city": "Suwanee",
    "state": "GA"
  },
  "createdByEmployee": false,
POST Create Draft 'Connect to Carrier Hotel' Switchboard Port Order
{{url}}/sdp/ordering/v1/api/sp/orders
Creates a new draft Switchboard Port order to 'Carrier Hotel'. It may be helpful to use the SDP UI as a guide when developing your first API request.

This is a multipart endpoint which consists of JSON (order) and multipart/form-data (attachment). The attachment is the required Letter of Authorization (LOA) from your carrier.

Required parameters for successful order submission (portProvider is the only required parameter for creation of draft order):

portProvider (CARRIER_HOTEL)
Carrier Hotel location id
salesforceContractID
virtualPortTypeId (GIG_E_1, GIG_E_10)
cableTypeId (SMF)
exchangeVisibility (false = private)
connectorTypeId (LC, SC)
Optional parameters for successful order submission (portProvider is the only required parameter for creation of draft order):

customerReferenceName
description
The required parameters can be obtained using the following requests:
/sdp/ordering/v1/api/sp/locations
/sdp/ordering/v1/api/contracts
/sdp/ordering/v1/api/sp/racks?location={datacenter site ID, ex. MIA1}
/sdp/ordering/v1/api/sp/carrier_hotel_locations

Submit Draft Switchboard orders using:
/sdp/ordering/v1/api/sp/orders/{order id}/submit

LOA attachment requirements:

content type must be multipart/form-data
key name must be LETTER_OF_AUTHORTIZATION
If using Postman, file must be stored locally
Following attachment document types are allowed:
pdf
txt
doc, docx
xls, xlsx
Maximum file size is 6 MB
Order attachment requirements:

content type must be JSON
key name must be order
You can copy the body shown in this documentation, replace your values and create a file using Notepad
If using Postman, file must be stored locally
Following attachment document types are allowed:
pdf
txt
doc, docx
xls, xlsx
Maximum file size is 6 MB
HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
  "portProvider": "CARRIER_HOTEL",
  "customerReferenceName": "Test Customer reference Name",
  "carrierHotelConfiguration": {
    "location": {
      "id": 7

    }
  },
  "salesforceContract": {
    "salesForceContractId": "8002F0000003oSIQAY"

  },
  "description": "Test Description",
  "physicalPort": {
    "spTypeId": "GIG_E_10",
   
    "cableTypeId": "SMF",
    "exchangeVisibility": false,
    "customerSide": {
      "connectorTypeId": "LC",
      "destinationType": "CONNECT_TO_CARRIER_HOTEL"
    }
  }
  
}


Example Request
Create Draft 'Connect to Carrier Hotel' Switchboard Port Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/sp/orders" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
  \"portProvider\": \"CARRIER_HOTEL\",
  \"customerReferenceName\": \"Test Customer reference Name\",
  \"carrierHotelConfiguration\": {
    \"location\": {
      \"id\": 7

    }
  },
  \"salesforceContract\": {
    \"salesForceContractId\": \"8002F0000003oSIQAY\"

  },
  \"description\": \"Test Description\",
  \"physicalPort\": {
    \"spTypeId\": \"GIG_E_10\",
   
    \"cableTypeId\": \"SMF\",
    \"exchangeVisibility\": false,
    \"customerSide\": {
      \"connectorTypeId\": \"LC\",
      \"destinationType\": \"CONNECT_TO_CARRIER_HOTEL\"
    }
  }
  
}"
POST Create & Submit New Switchboard Port Order 
{{url}}/sdp/ordering/v1/api/sp/orders/submit
Create and submit a new Switchboard Port order.

Valid order details must be specified in request body.

Status of order can be confirmed using:
/sdp/ordering/v1/api/sp/orders/657/action_history

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
  "location": {
    "id": 66482,
    "name": "ATL1"
  },
  "expeditedOrder": false,
  "physicalPort": {
    "spTypeId": "GIG_E_1",
    "cableTypeId": "SMF",
    "exchangeVisibility": true,
    "customerSide": {
      "connectorTypeId": "LC",
      "rackId": "f962d958db2f2340d655fd33399619fa",
      "endpointType": "TERMINATE_TO_RACK"
    }
  },
  "portProvider": "QTS",
  "salesforceContract": {
    "salesForceContractId": "8002F0000003oSIQAY"
  },
  "diverseFrom": {
    "id": 620,
    "number": "SP-000000226"
  }
}


Example Request
Create & Submit New Switchboard Port Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/sp/orders/submit" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
  \"location\": {
    \"id\": 3289815,
    \"name\": \"CHI1\",
    \"city\": \"Chicago\",
    \"state\": \"IL\"
  },
\"salesforceContract\": {
\"salesForceContractId\": \"800q0000000N5fzAAC\"
},
  \"expeditedOrder\": true,
  \"description\": \"Description\",
  \"customerReferenceName\": \"Customer Refence Name\",
  \"physicalPort\": {
    \"exchangePortTypeId\": \"GIG_E_1\",
    \"exchangePortTypeDescription\": \"1 Gig-E\",
    \"cableTypeId\": \"SMF\",
    \"cableTypeDescription\": \"SMF\",
    \"exchangeVisibility\": true,
    \"customerSide\": {
      \"connectorTypeId\": \"LC\",
      \"rackId\": \"8b33a348db124300a571ff851d9619e6\",
      \"rackName\": \"ENC-CHI1-DC1-1-DH1-KG16\",
      \"endpointType\": \"PATCH_PANEL\",
      \"patchPanel\": \"PP name\",
      \"port\": \"Port\",
      \"comment\": \"Comments\",
      \"destinationType\": \"CONNECT_TO_MY_CAGE\",
      \"panelShelf\": \"Panel/Shelf\",
      \"portBlock\": \"Port/Block\"
    }
  }
}"
Example Response
－
{
  "id": 657,
  "number": "SP-000000255",
  "created": "2019-05-01T18:20:33.305 +0000",
  "location": {
    "id": 66482,
    "name": "ATL1",
    "city": "Atlanta",
    "state": "GA"
  },
  "createdByEmployee": false,
POST Validate Existing Switchboard Port Order 
{{url}}/sdp/ordering/v1/api/sp/orders/655/validate
Validates details of draft Switchboard order prior to submission. No new order is created nor is update performed.

OrderID must be included in URL. Results will show Swithcboard Port Details that are required for successful submission of order when General parameters are present in the draft order.

200 OK response means order details are valid and can be used for new Switchboard order creation.

OrderID can be obtained using:
/sdp/ordering/v1/api/sp/orders

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Validate Existing Switchboard Port Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/sp/orders/655/validate" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data ""
Example Response
400 － Bad Request
{
  "code": "INVALID_INPUT_DATA",
  "message": "portBlock: may not be empty\nconnectorTypeId: may not be null\npanelShelf: may not be empty\nrackId: may not be null\npatchPanel: may not be empty"
}
POST Validate New Switchboard Port Order 
{{url}}/sdp/ordering/v1/api/sp/orders/validate
Validates details of new Switchboard order prior to submission. No new order is created nor is update performed. Results will show parameter that does not meet requirement. 200 OK response means order details are valid and can be used for new Switchboard order creation.

For list of required parameters, see documentation on type of order (ie Terminate to Rack, Connect to Patch Panel, Connect to Carrier Hotel)

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
"location": {
"id": 66483
},
"salesforceContract": {
"salesForceContractId": "800q0000000N5fzAAC"
},
"expeditedOrder": true,
"description": "test description",
"customerReferenceName": "All fields test",
"physicalPort": {
"virtualPortTypeId": "GIG_E_1",
"cableTypeId": "SMF",
"exchangeVisibility": true,
"customerSide": {
"connectorTypeId": "SC",
"rackId": "ff0f612cdba02bc0b300001b8a961909",
"endpointType": "TERMINATE_TO_RACK",
"comment": "All fields comment"
}
}
}


Example Request
Validate New Switchboard Port Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/sp/orders/validate" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
  \"location\": {
    \"id\": 66482,
    \"name\": \"ATL1\"
  },
  \"expeditedOrder\": false,
  \"physicalPort\": {
    \"spTypeId\": \"GIG_E_1\",
    \"cableTypeId\": \"SMF\",
    \"exchangeVisibility\": true,
    \"customerSide\": {
      \"connectorTypeId\": \"LC\",
      \"rackId\": \"f962d958db2f2340d655fd33399619fa\",
      \"endpointType\": \"TERMINATE_TO_RACK\"
    }
  },
  \"portProvider\": \"QTS\",
  \"salesforceContract\": {
    \"salesForceContractId\": \"8002F0000003oSIQAY\"
  },
  \"diverseFrom\": {
    \"id\": 620,
    \"number\": \"SP-000000226\"
  }
}"
POST Resubmit Switchboard Port order 
{{url}}/sdp/ordering/v1/api/sp/orders/resubmit
Resubmits Switchboard order that is currently in 'Need More Information' status to update details and comments on order for DCO.

For list of required parameters, see documentation on type of order (ie Terminate to Rack, Connect to Patch Panel, Connect to Carrier Hotel)

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
"order" :{
"id": 10894,
"number": "VP-000000405",
"location": {"id": 3289737},
"salesforceContract": {"salesForceContractId": "800q0000000N5fzAAC"},
"physicalPort": {
"virtualPortTypeId": "GIG_E_1",
"cableTypeId": "SMF",
"customerSide": {"connectorTypeId": "SC","rackId": "ff0f612cdba02bc0b300001b8a961909","endpointType": "PATCH_PANEL","patchPanel": "API PP","panelShelf" : "API CPS","portBlock": "API CPB"}
}
},
"comment" : "resubmit reason"
}


Example Request
Resubmit Switchboard Port order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/sp/orders/resubmit" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
\"order\" :{
\"id\": 10894,
\"number\": \"VP-000000405\",
\"location\": {\"id\": 3289737},
\"salesforceContract\": {\"salesForceContractId\": \"800q0000000N5fzAAC\"},
\"physicalPort\": {
\"virtualPortTypeId\": \"GIG_E_1\",
\"cableTypeId\": \"SMF\",
\"customerSide\": {\"connectorTypeId\": \"SC\",\"rackId\": \"ff0f612cdba02bc0b300001b8a961909\",\"endpointType\": \"PATCH_PANEL\",\"patchPanel\": \"API PP\",\"panelShelf\" : \"API CPS\",\"portBlock\": \"API CPB\"}
}
},
\"comment\" : \"resubmit reason\"
}"
POST Cancel Switchboard Port Order 
{{url}}/sdp/ordering/v1/api/sp/orders/cancel
Cancels existing Switchboard order.

Required parameters:

OrderId
CancelUserComment
The following order statuses can be cancelled:

Draft
Awaiting Internal Review
Need More Information
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"orderId":657,
"cancelUserComment":"Cancel SP order as part of testing process"
}


Example Request
Cancel Switchboard Port Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/sp/orders/cancel" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"orderId\":657,
\"cancelUserComment\":\"Cancelled SP order as part of testing\"
}"
POST Update & Submit Existing Switchboard Port Order 
{{url}}/sdp/ordering/v1/api/vp/orders/<order id>/submit
Update and submit existing Swithboard Port order. If update is not required (submit order only), put {} in the body with no additional details.

Order id must be specified in URL.

For list of required parameters, see documentation on type of order (ie Terminate to Rack, Connect to Patch Panel, Connect to Carrier Hotel)

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
  "id": 13381,
  "location": {
    "id": 3289814,
    "name": "ATL1",
    "city": "Atlanta",
    "state": "GA"
  },
  "salesforceContract": {
    "salesForceContractId": "8003B000000O2PDQA0",
    "number": "00007885",
    "gpNumber": "GPIrinaContractATL1",
    "location": "ATL1",
    "startDate": "11-09-2018",
    "endDate": "11-09-2019"
  },
  "expeditedOrder": true,
  "description": "Description_updated",
  "customerReferenceName": "Customer Refence Name_updated",
  "physicalPort": {
    "exchangePortTypeId": "GIG_E_1",
    "exchangePortTypeDescription": "1 Gig-E",
    "cableTypeId": "SMF",
    "cableTypeDescription": "SMF",
    "exchangeVisibility": true,
    "customerSide": {
      "connectorTypeId": "LC",
      "rackId": "84b0b8596fe215400247af512e3ee47c",
      "rackName": "ENC-MIA1-DC1-1-POD2-208-05-Rack QVI03",
      "endpointType": "PATCH_PANEL",
      "patchPanel": "PP name",
      "port": "Port",
      "comment": "Comments",
      "destinationType": "CONNECT_TO_MY_CAGE",
      "panelShelf": "Panel/Shelf",
      "portBlock": "Port/Block"
    }
  }
}


Example Request
Update & Submit Existing Switchboard Port Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vp/orders/%3Corder%20id%3E/submit" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
  \"id\": 13381,
  \"location\": {
    \"id\": 3289814,
    \"name\": \"ATL1\",
    \"city\": \"Atlanta\",
    \"state\": \"GA\"
  },
  \"salesforceContract\": {
    \"salesForceContractId\": \"8003B000000O2PDQA0\",
    \"number\": \"00007885\",
    \"gpNumber\": \"GPIrinaContractATL1\",
    \"location\": \"ATL1\",
    \"startDate\": \"11-09-2018\",
    \"endDate\": \"11-09-2019\"
  },
  \"expeditedOrder\": true,
  \"description\": \"Description_updated\",
  \"customerReferenceName\": \"Customer Refence Name_updated\",
  \"physicalPort\": {
    \"exchangePortTypeId\": \"GIG_E_1\",
    \"exchangePortTypeDescription\": \"1 Gig-E\",
    \"cableTypeId\": \"SMF\",
    \"cableTypeDescription\": \"SMF\",
    \"exchangeVisibility\": true,
    \"customerSide\": {
      \"connectorTypeId\": \"LC\",
      \"rackId\": \"84b0b8596fe215400247af512e3ee47c\",
      \"rackName\": \"ENC-MIA1-DC1-1-POD2-208-05-Rack QVI03\",
      \"endpointType\": \"PATCH_PANEL\",
      \"patchPanel\": \"PP name\",
      \"port\": \"Port\",
      \"comment\": \"Comments\",
      \"destinationType\": \"CONNECT_TO_MY_CAGE\",
      \"panelShelf\": \"Panel/Shelf\",
      \"portBlock\": \"Port/Block\"
    }
  }
}"
PUT Update Draft Switchboard Port order 
{{url}}/sdp/ordering/v1/api/sp/orders/<order id>
Updates existing draft Switchboard Port order details. Order ID must be specified in URL. Order details must be specified in the body of the request.

For list of required parameters, see documentation on type of order (ie Terminate to Rack, Connect to Patch Panel, Connect to Carrier Hotel)

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
"location": {
"id": 3289737
},
"salesforceContract": {
"salesForceContractId": "800q0000000N5fzAAC"
},
"expeditedOrder": true,
"description": "test description",
"customerReferenceName": "All fields test",
"physicalPort": {
"virtualPortTypeId": "GIG_E_1",
"cableTypeId": "SMF",
"exchangeVisibility": true,
"customerSide": {
"connectorTypeId": "LC",
"rackId": "ff0f612cdba02bc0b300001b8a961909",
"endpointType": "PATCH_PANEL",
"patchPanel": "All fields PP",
"port": "All fields port",
"panelShelf" : "All fields CPS",
"portBlock": "All fields CPB",
"comment": "All fields comment"
}
}
}


Example Request
Update Draft Switchboard Port order
curl --location --request PUT "{{url}}/sdp/ordering/v1/api/sp/orders/621" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
\"portProvider\": \"QTS\",
\"description\": \"test update to exchange visibility to private\",
\"exchangeVisibility\": false
}"
Example Response
200 － OK
{
  "id": 621,
  "number": "SP-000000227",
  "created": "2019-04-24T17:36:46.041 +0000",
  "createdByEmployee": false,
  "cpy": "CPY000000005589",
  "expeditedOrder": false,
  "sideA": {
    "destinationType": "CONNECT_TO_CARRIER"
  },
  "sideZ": {
DEL Delete Draft Switchboard Port Order
{{url}}/sdp/ordering/v1/api/sp/orders/<order ID>
Deletes draft Switchboard Port order. Order ID must be specified in URL.

Only draft orders can be deleted.

Order ID can be obtained using:
/sdp/ordering/v1/api/sp/orders

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Delete Draft Switchboard Port Order
curl --location --request DELETE "{{url}}/sdp/ordering/v1/api/sp/orders/629" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data ""
Switchboard Virtual Connect

GET Get List of virtualConnect Orders 
{{url}}/sdp/ordering/v1/api/vc/orders?pageNumber=1&pageSize=10
Returns list of Virtual Connect orders.

Page filters are used in response; ensure your page size is correct.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
pageNumber1
pageSize10

Example Request
Get VC order list
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/orders?pageNumber=1&pageSize=10" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
    "pageInfo": {
        "pageSize": 10,
        "totalCount": 25,
        "totalPages": 3,
        "pageNumber": 1
    },
    "data": [
        {
            "id": 459,
            "number": "VC-000000131",
GET Get virtualConnect Order Details 
{{url}}/sdp/ordering/v1/api/vc/orders/{VC order ID}
Returns virtualConnect order details. virtualConnect order ID must be specified in the request URL.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get virtualConnect Order Details
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/orders/601" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "id": 601,
  "number": "VC-000000228",
  "created": "2019-04-11T20:10:49.573 +0000",
  "location": {
    "id": 66492,
    "name": "SUW1",
    "city": "Suwanee",
    "state": "GA"
  },
  "createdByEmployee": true,
GET Get Occupied VLANs per Switchboard Port 
{{url}}/sdp/ordering/v1/api/vc/existing_vlans
Returns list of VLANs occupied by Switchboard ports.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get Occupied VLANs per Switchboard Port
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/existing_vlans" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "452": [
    30
  ],
  "455": [
    2342,
    2345,
    54,
    2333,
    1234,
    1436,
GET Get virtualConnect Order Status & History 
{{url}}/sdp/ordering/v1/api/vc/orders/{VC order ID}/action_history
Returns status and history of applied actions for specific virtualConnect orders. virtualConnect order ID must be specified in URL.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get virtualConnect Order Status & History
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/orders/601/action_history" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "orderActionHistory": [
    {
      "comment": "",
      "author": "QTS Employee",
      "operationDate": "2019-04-11T20:10:49.887+0000",
      "action": "SUBMITTED"
    },
    {
      "comment": "",
      "author": "QTS Employee",
GET Get List of Available Cloud Connection Speed for Location 
{{url}}/sdp/ordering/v1/api/vc/supported_cloud_speeds?locationId=66492
Returns available speed values (1Gbps or 10Gbps) for specific locationId (data center). Location ID must be specified as URL parameter.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
locationId66492

Example Request
List of Available Cloud Connection Speed for Location
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/supported_cloud_speeds?locationId=66492" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  "1Gbps"
]
GET Get List of Data Centers that Support Cloud Connections 
{{url}}/sdp/ordering/v1/api/vc/supported_cloud_locations
Rreturns list of Data Centers that support Cloud Connections. Virtual Connect with connection type of "Cloud" must be created from one of these locations only.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
List of Data Centers that Support Cloud Connections
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/supported_cloud_locations" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "id": 66492,
    "name": "SUW1",
    "city": "Suwanee",
    "state": "GA"
  },
  {
    "id": 66483,
    "name": "CHI1",
    "city": "Chicago",
GET Download Attachment from virtualConnect Order 
{{url}}/sdp/ordering/v1/api/vc/orders/attachments/{attachment id}
Downloads attachment of a virtualConnect order by attachment ID.

Attachment id can be obtained using:
/sdp/ordering/v1/api/vc/orders/{VC order ID}

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Download Attachment from virtualConnect Order
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/orders/attachments/%7Battachment%20id%7D" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Get Cloud Destination Regions (AWS) 
{{url}}/sdp/ordering/v1/api/vc/aws_destination_regions
Returns list of destination regions for AWS Hosted Cloud.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get Cloud Destination Regions (AWS)
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/aws_destination_regions" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "region": "us-east-1",
    "description": "US East 1 (North Virginia)",
    "alias": "US East (N. Virginia)"
  },
  {
    "region": "us-west-1",
    "description": "US West 1 (North California)",
    "alias": "US West (N. California)"
  },
GET Get Cloud Destination Regions (Azure / GCP) 
{{url}}/sdp/ordering/v1/api/vc/cloud_destination_regions?provider=GOOGLE_HOSTED
Returns list of destination regions for Azure and Google Cloud.

Cloud Provider must be specified in the URL as parameter:

?provider=AZURE_HOSTED
?provider=GOOGLE_HOSTED
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
providerGOOGLE_HOSTED

Example Request
Get Cloud Destination Regions (Azure / GCP)
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/cloud_destination_regions?provider=AZURE_HOSTED" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "id": 211,
    "name": "WDC",
    "description": "Washington DC",
    "alias": null
  },
  {
    "id": 205,
    "name": "SFO",
    "description": "San Francisco / Silicon Valley",
GET Generate New BGP Key 
{{url}}/sdp/ordering/v1/api/vc/bgp_key
Generates new random BGP key value for AWS Hosted connections.

You can use your own BGP keys for AWS Hosted connections.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Generate New BGP Key
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/bgp_key" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "value": "G7X0cJ9pWkG3xpwrKxifTqvI"
}
GET Get virtualConnect Order Estimated Price 
{{url}}/sdp/ordering/v1/api/vc/estimate_price?location=MIA1&locationZ=ATL1&speed=GIG_E_1
Returns estimated price for Monthly payments for virtual connect.

Supported parameters:

?location=MIA1&locationZ=ATL1&speed=GIG_E_1
?location=MIA1&connectionToCloud=true&provider=AWS_HOSTED&speed=GIG_E_10
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
locationMIA1
locationZATL1
speedGIG_E_1

Example Request
Get virtualConnect Order Estimated Price
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/estimate_price?location=MIA1&locationZ=ATL1&speed=GIG_E_1" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "monthlyPrice": 200,
  "setupCharge": 500
}
GET Get Available Speed Options for Specific Location 
{{url}}/sdp/ordering/v1/api/vc/supported_ep_speeds?clientId=123&locationId=3289814
This method returns available speed values for specific Data Center. Location ID should be specified as URL parameter. Client ID for Client that you'd like to connect to must be specified as URL parameter.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
clientId123
locationId3289814

Example Request
Get Available Speed Options for Specific Location
curl --location --request GET "{{url}}/sdp/ordering/v1/api/vc/supported_ep_speeds?clientId=123&locationId=3289814" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
POST Create & Submit New virtualConnect Order 
{{url}}/sdp/ordering/v1/api/vc/orders/submit
Creates a new 'Draft' VC order to one of your public Exchange ports and submits the order in one operation.

This endpoint consumes multipart/form-data which consists of JSON part and attachment parts.

JSON part must be named 'VC_ORDER' and must have a content type of JSON.

VC_ORDER example:

{ "sideA": { "customerReferenceName": "test", "vlan": 7, "description": "Test Description", "epOrder": { "id": 10675, "location": { "id": 3289737 } } }, "connectionTo": { "id": 1910 }, "location": { "id": 3289737 } }

Attachment part must be named 'LETTER_OF_AUTHORIZATION' and must have content type of form-data>file.

Following document types are allowed:

pdf
txt
doc, docx
xls, xlsx
Maximum file size is 6 MB
HEADERS
Content-Typeapplication/x-www-form-urlencoded
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
LETTER_OF_AUTHORIZATION'
VC_ORDER

Example Request
Create & Submit New virtualConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders/submit" \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --form "LETTER_OF_AUTHORIZATION'=@" \
  --form "VC_ORDER=@"
POST Submit Existing virtualConnect Order 
{{url}}/sdp/ordering/v1/api/vc/orders/{VC order id}/submit
This method allows to submit existing VC order. VC order id must be specified in URL.

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Submit Existing virtualConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders/%7BVC%20order%20id%7D/submit" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data ""
POST Update Existing virtualConnect Order 
{{url}}/sdp/ordering/v1/api/vc/orders/{VC order id}
This API allows to update existing 'Draft' VC order. VC order ID must be specified in URL.

If you are willing to update VC order details but not update attachment for the order - you can set request body format to JSON, post full VC order detials and current attachment documents must be listed as "attachedFiles": [] array.

If you are willing to update order details AND update list of attachmed documents - requst body part should be set to multipart/form-data which consists of JSON part and attachment parts. JSON part has name 'VC_ORDER' Attachment part names: 'LETTER_OF_AUTHORIZATION' Following document types are allowed: pdf, txt, doc, docx, xlsx, xls. Maximum file size is 6 MB

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
  "id": 13429,
  "connectionType": "CLOUD",
  "location": {
    "id": 3289815
  },
  "sideA": {
    "customerReferenceName": "Test Customer reference Name",
    "vlan": 56,
    "description": "Description for VC Type-4 'Cloud' connection order to AWS Hosted cloud provider",
    "epOrder": {
      "id": 12262,
      "location": {
        "id": 3289815
      }
    }
  },
  "cloudConnect": true,
  "cloudDetails": {
    "cloudServiceProvider": "AWS_HOSTED",
    "publicConnection": true,
    "destinationRegion": {
      "region": "us-east-1",
      "description": "US East 1 (North Virginia)"
    },
    "awsAccountId": "Amazon Account ID",
    "asn": 64513,
    "bgpKey": "yVPDRoYVMMRMWnXscO7P0BL3",
    "customerRouterIP": "10.10.21.13/21",
    "amazonRouterIP": "10.10.21.14/21",
    "prefixesToAdvertise": "10.10.21.0/24,65.65.65.0/24"
  },
  "speed": "GIG_E_1",
  "attachedFiles": []
}


Example Request
Update Existing virtualConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders/%7BVC%20order%20id%7D" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
  \"id\": 13429,
  \"connectionType\": \"CLOUD\",
  \"location\": {
    \"id\": 3289815
  },
  \"sideA\": {
    \"customerReferenceName\": \"Test Customer reference Name\",
    \"vlan\": 56,
    \"description\": \"Description for VC Type-4 'Cloud' connection order to AWS Hosted cloud provider\",
    \"epOrder\": {
      \"id\": 12262,
      \"location\": {
        \"id\": 3289815
      }
    }
  },
  \"cloudConnect\": true,
  \"cloudDetails\": {
    \"cloudServiceProvider\": \"AWS_HOSTED\",
    \"publicConnection\": true,
    \"destinationRegion\": {
      \"region\": \"us-east-1\",
      \"description\": \"US East 1 (North Virginia)\"
    },
    \"awsAccountId\": \"Amazon Account ID\",
    \"asn\": 64513,
    \"bgpKey\": \"yVPDRoYVMMRMWnXscO7P0BL3\",
    \"customerRouterIP\": \"10.10.21.13/21\",
    \"amazonRouterIP\": \"10.10.21.14/21\",
    \"prefixesToAdvertise\": \"10.10.21.0/24,65.65.65.0/24\"
  },
  \"speed\": \"GIG_E_1\",
  \"attachedFiles\": []
}"
POST Create New virtualConnect Cloud Order to GCP (no attachment) 
{{url}}/sdp/ordering/v1/api/vc/orders
This API allows to create new 'Draft' VC 'Cloud' - GCP order from one of your Switchboard port orders (with no attachment).

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{"connectionType":"CLOUD","location":{"id":3289815},"sideA":{"customerReferenceName":"Test Customer reference Name","vlan":67,"epOrder":{"id":12262,"location":{"id":3289815}}},"cloudConnect":true,"cloudDetails":{"cloudServiceProvider":"GOOGLE_HOSTED","destinationRegion":{"id":178,"name":"CHI","description":"Chicago"},"gcpVlanAttachmentName":"attachment Name","pairingKey":"37dbb53f-db95-41cc-90cb-1f3496337e56/us-central1/1"},"speed":"GIG_E_1"}


Example Request
Create New virtualConnect Cloud Order to GCP (no attachment)
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{\"connectionType\":\"CLOUD\",\"location\":{\"id\":3289815},\"sideA\":{\"customerReferenceName\":\"Test Customer reference Name\",\"vlan\":67,\"epOrder\":{\"id\":12262,\"location\":{\"id\":3289815}}},\"cloudConnect\":true,\"cloudDetails\":{\"cloudServiceProvider\":\"GOOGLE_HOSTED\",\"destinationRegion\":{\"id\":178,\"name\":\"CHI\",\"description\":\"Chicago\"},\"gcpVlanAttachmentName\":\"attachment Name\",\"pairingKey\":\"37dbb53f-db95-41cc-90cb-1f3496337e56/us-central1/1\"},\"speed\":\"GIG_E_1\"}
"
POST Create New virtualConnect Cloud Order to Azure (no attachment) 
{{url}}/sdp/ordering/v1/api/vc/orders
This API allows to create new 'Draft' VC 'Cloud' - Azure Hosted order from one of your Switchboard port orders (with no attachment).

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{"connectionType":"CLOUD","location":{"id":3289815},"sideA":{"customerReferenceName":"Test Customer reference Name","vlan":67,"description":"Description for VC Type-4 'Cloud' Azure Hosted connection","epOrder":{"id":12262,"location":{"id":3289815}}},"cloudConnect":true,"cloudDetails":{"cloudServiceProvider":"AZURE_HOSTED","azureServiceKey":"Azure Service Key","destinationRegion":{"id":178,"name":"CHI","description":"Chicago"},"microsoftPeering":true,"privatePeering":true,"microsoftPeeringVlan":68},"speed":"GIG_E_1"}


Example Request
Create New virtualConnect Cloud Order to Azure (no attachment)
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{\"connectionType\":\"CLOUD\",\"location\":{\"id\":3289815},\"sideA\":{\"customerReferenceName\":\"Test Customer reference Name\",\"vlan\":67,\"description\":\"Description for VC Type-4 'Cloud' Azure Hosted connection\",\"epOrder\":{\"id\":12262,\"location\":{\"id\":3289815}}},\"cloudConnect\":true,\"cloudDetails\":{\"cloudServiceProvider\":\"AZURE_HOSTED\",\"azureServiceKey\":\"Azure Service Key\",\"destinationRegion\":{\"id\":178,\"name\":\"CHI\",\"description\":\"Chicago\"},\"microsoftPeering\":true,\"privatePeering\":true,\"microsoftPeeringVlan\":68},\"speed\":\"GIG_E_1\"}
"
POST Create New virtualConnect Cloud Order to AWS (no attachment) 
{{url}}/sdp/ordering/v1/api/vc/orders
This API allows to create new 'Draft' VC 'Cloud' - AWS Hosted order from one of your Switchboard port orders (with no attachment). For 'public' connections additional parameters must be specified: "customerRouterIP": "10.10.21.13/21", "amazonRouterIP": "10.10.21.14/21", "prefixesToAdvertise": "10.10.21.0/24,65.65.65.0/24"

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{"connectionType":"CLOUD","location":{"id":3289815},"sideA":{"customerReferenceName":"Test Customer reference Name","vlan":56,"description":"Description for VC Type-4 'Cloud' connection order to AWS Hosted cloud provider","epOrder":{"id":12262,"location":{"id":3289815}}},"cloudConnect":true,"cloudDetails":{"cloudServiceProvider":"AWS_HOSTED","publicConnection":false,"destinationRegion":{"region":"us-east-1","description":"US East 1 (North Virginia)"},"awsAccountId":"Amazon Account ID","asn":64513,"bgpKey":"yVPDRoYVMMRMWnXscO7P0BL3"},"speed":"GIG_E_1"}


Example Request
Create New virtualConnect Cloud Order to AWS (no attachment)
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{\"connectionType\":\"CLOUD\",\"location\":{\"id\":3289815},\"sideA\":{\"customerReferenceName\":\"Test Customer reference Name\",\"vlan\":56,\"description\":\"Description for VC Type-4 'Cloud' connection order to AWS Hosted cloud provider\",\"epOrder\":{\"id\":12262,\"location\":{\"id\":3289815}}},\"cloudConnect\":true,\"cloudDetails\":{\"cloudServiceProvider\":\"AWS_HOSTED\",\"publicConnection\":false,\"destinationRegion\":{\"region\":\"us-east-1\",\"description\":\"US East 1 (North Virginia)\"},\"awsAccountId\":\"Amazon Account ID\",\"asn\":64513,\"bgpKey\":\"yVPDRoYVMMRMWnXscO7P0BL3\"},\"speed\":\"GIG_E_1\"}
"
POST Create New virtualConnect Long Haul Order (no attachment) 
{{url}}/sdp/ordering/v1/api/vc/orders
This API allows to create new 'Draft' VC 'Long Haul' order from one of your Switchboard port orders (with no attachment).

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{"connectionType":"LONG_HAUL","location":{"id":3289815},"sideA":{"customerReferenceName":"Test Customer reference Name","vlan":57,"description":"Description for VC Order Type-3 'Long Haul'","epOrder":{"id":12262,"location":{"id":3289815}}},"cloudConnect":false,"connectionTo":{"id":1910},"locationZ":{"id":3289814},"speed":"GIG_E_10"}


Example Request
Create New virtualConnect Long Haul Order (no attachment)
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{\"connectionType\":\"LONG_HAUL\",\"location\":{\"id\":3289815},\"sideA\":{\"customerReferenceName\":\"Test Customer reference Name\",\"vlan\":57,\"description\":\"Description for VC Order Type-3 'Long Haul'\",\"epOrder\":{\"id\":12262,\"location\":{\"id\":3289815}}},\"cloudConnect\":false,\"connectionTo\":{\"id\":1910},\"locationZ\":{\"id\":3289814},\"speed\":\"GIG_E_10\"}
"
POST Create New virtualConnect Metro Order (no attachment) 
{{url}}/sdp/ordering/v1/api/vc/orders
This API allows to create new 'Draft' VC 'Local' order from one of your Switchboard port orders (with no attachment). Please note you can specify Swithcboard ports of your Company as Z-side: "sideZ": { "vlan": 67, "epOrder": { "id": 11967 } In this case sideA.vlan must be specified as well. In case connection to other Client (from the list of available Clients) is expected, specify Z-side as client id: "locationZ": { "id": 3289907 },

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
  "connectionType": "METRO",
  "location": {
    "id": 3289814
  },
  "sideA": {
    "customerReferenceName": "Test Customer reference Name",
    "vlan": 56,
    "description": "Description for VC Type-3 'Metro' connection",
    "epOrder": {
      "id": 10692,
      "location": {
        "id": 3289814
      }
    }
  },
  "cloudConnect": false,
  "connectionTo": {
    "id": 1910
  },
  "locationZ": {
    "id": 3289907
  },
  "speed": "GIG_E_10",
  "sideZ": {
    "vlan": 67,
    "epOrder": {
      "id": 11967
    }
  }
}


Example Request
Create New virtualConnect Metro Order (no attachment)
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
  \"connectionType\": \"METRO\",
  \"location\": {
    \"id\": 3289814
  },
  \"sideA\": {
    \"customerReferenceName\": \"Test Customer reference Name\",
    \"vlan\": 56,
    \"description\": \"Description for VC Type-3 'Metro' connection\",
    \"epOrder\": {
      \"id\": 10692,
      \"location\": {
        \"id\": 3289814
      }
    }
  },
  \"cloudConnect\": false,
  \"connectionTo\": {
    \"id\": 1910
  },
  \"locationZ\": {
    \"id\": 3289907
  },
  \"speed\": \"GIG_E_10\",
  \"sideZ\": {
    \"vlan\": 67,
    \"epOrder\": {
      \"id\": 11967
    }
  }
}"
POST Create New virtualConnect Local Order (no attachment) 
{{url}}/sdp/ordering/v1/api/vc/orders
This API allows to create new 'Draft' VC 'Local' order from one of your Switchboard port orders (with no attachment).

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{"connectionType":"LOCAL","location":{"id":3289737},"sideA":{"customerReferenceName":"Test Customer reference Name","vlan":46,"description":"Test Description for Type-1 Local VC order","epOrder":{"id":10398,"location":{"id":3289737}}},"cloudConnect":false,"connectionTo":{"id":1645},"locationZ":{"id":3289737},"speed":"GIG_E_1"}


Example Request
Create New virtualConnect Local Order (no attachment)
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{\"connectionType\":\"LOCAL\",\"location\":{\"id\":3289737},\"sideA\":{\"customerReferenceName\":\"Test Customer reference Name\",\"vlan\":46,\"description\":\"Test Description for Type-1 Local VC order\",\"epOrder\":{\"id\":10398,\"location\":{\"id\":3289737}}},\"cloudConnect\":false,\"connectionTo\":{\"id\":1645},\"locationZ\":{\"id\":3289737},\"speed\":\"GIG_E_1\"}
"
POST Create New virtualConnect Local Order (with attachment) 
{{url}}/sdp/ordering/v1/api/vc/orders
This API allows to create new 'Draft' VC 'Local' order from one of your Switchboard port orders.

Please note that this endpoint consumes multipart/form-data which consists of JSON part and attachment parts.

JSON part has name 'VC_ORDER' JSON part example:

{"connectionType":"LOCAL","location":{"id":3289737},"sideA":{"customerReferenceName":"Test Customer reference Name","vlan":46,"description":"Test Description for Type-1 Local VC order","epOrder":{"id":10398,"location":{"id":3289737}}},"cloudConnect":false,"connectionTo":{"id":1645},"locationZ":{"id":3289737},"speed":"GIG_E_1"}

Attachment part names: 'LETTER_OF_AUTHORIZATION' Following document types are allowed: pdf, txt, doc, docx, xlsx, xls. Maximum file size is 6 MB

HEADERS
Content-Typeapplication/x-www-form-urlencoded
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
LETTER_OF_AUTHORIZATION
VC_ORDER

Example Request
Create New virtualConnect Local Order (with attachment)
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders" \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --form "LETTER_OF_AUTHORIZATION=@" \
  --form "VC_ORDER=@"
POST Reconfigure VLAN for virtualConnect Order 
{{url}}/sdp/ordering/v1/api/vc/orders/{VC order id}/update_cvlan
This API request initiates VLAN reconfiguration for already installed Virtual Connects (with Active/Fulfilled) status. VC order ID must be specified as URL parameter. VLAN values must be specified as request body.

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
"cVlanSideA":123,
"cVlanSideZ":null,
"privatePeeringVlan": null,
"microsoftPeeringVlan":null
}


Example Request
Reconfigure VLAN for virtualConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders/%7BVC%20order%20id%7D/update_cvlan" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
\"cVlanSideA\":123,
\"cVlanSideZ\":null,
\"privatePeeringVlan\": null,
\"microsoftPeeringVlan\":null
}"
POST Validate Azure Service Key 
{{url}}/sdp/ordering/v1/api/vc/azure_service_key/{key}/validate
If you're willing to validate Azure Service Key - this API would return service key status. Note: in case Azure key specified for you VC Type-4 'Cloud' connection to Azure Hosted is not valid, SDP would not be able to confire virtual connection.

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Validate Azure Service Key
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/azure_service_key/%7Bkey%7D/validate" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data ""
POST Approve Incoming virtualConnect Order 
{{url}}/sdp/ordering/v1/api/vc/orders/approve
This API allows to approve incoming VC order to one of your public Exchange ports.

Please note that this endpoint consumes multipart/form-data which consists of JSON part and attachment parts.

JSON part has name 'VC_ORDER' JSON part example:

{ "vcOrder": { "id": 1651, "connectionTo": { "id": 1440 } }, "sideZ": { "customerReferenceName": "test-1", "vlan": 8, "description": "Description description.", "vpOrder": { "id": 1697 } }

Attachment part names: 'LETTER_OF_AUTHORIZATION' Following document types are allowed: pdf, txt, doc, docx, xlsx, xls. Maximum file size is 6 MB

HEADERS
Content-Typeapplication/x-www-form-urlencoded
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
LETTER_OF_AUTHORIZATION'
VC_ORDER

Example Request
Approve Incoming virtualConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders/approve" \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --form "LETTER_OF_AUTHORIZATION'=@" \
  --form "VC_ORDER="
POST Cancel virtualConnect Order 
{{url}}/sdp/ordering/v1/api/vc/orders/cancel
Virtual Connect Order that was submitted and not approved by Z-side Customer could be cancelled by executing this API request.

Order ID and cancellation reason must be provided as request body.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"id":10528,
"comment":"Cancelled VC as part of acceptance process"
}


Example Request
Cancel virtualConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders/cancel" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"id\":10528,
\"comment\":\"Cancelled VC as part of acceptance process\"
}"
POST Reject Incoming virtualConnect Order 
{{url}}/sdp/ordering/v1/api/vc/orders/reject
This method allows to reject incoming VC order, VC order id and rejection reason must be provided as request body.

HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
"id": 1651,
"comment": "Reject description."
}


Example Request
Reject Incoming virtualConnect Order
curl --location --request POST "{{url}}/sdp/ordering/v1/api/vc/orders/reject" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
\"id\": 1651,
\"comment\": \"Reject description.\"
}"
DEL Delete Draft virtualConnect Order
{{url}}/sdp/ordering/v1/api/vc/orders/5
HEADERS
Content-Typeapplication/json
AuthorizationBearer {{ACCESS_TOKEN}}
BODY
{
  "id": 13381,
  "location": {
    "id": 3289814,
    "name": "ATL1",
    "city": "Atlanta",
    "state": "GA"
  },
  "salesforceContract": {
    "salesForceContractId": "8003B000000O2PDQA0",
    "number": "00007885",
    "gpNumber": "GPIrinaContractATL1",
    "location": "ATL1",
    "startDate": "11-09-2018",
    "endDate": "11-09-2019"
  },
  "expeditedOrder": true,
  "description": "Description_updated",
  "customerReferenceName": "Customer Refence Name_updated",
  "physicalPort": {
    "exchangePortTypeId": "GIG_E_1",
    "exchangePortTypeDescription": "1 Gig-E",
    "cableTypeId": "SMF",
    "cableTypeDescription": "SMF",
    "exchangeVisibility": true,
    "customerSide": {
      "connectorTypeId": "LC",
      "rackId": "84b0b8596fe215400247af512e3ee47c",
      "rackName": "ENC-MIA1-DC1-1-POD2-208-05-Rack QVI03",
      "endpointType": "PATCH_PANEL",
      "patchPanel": "PP name",
      "port": "Port",
      "comment": "Comments",
      "destinationType": "CONNECT_TO_MY_CAGE",
      "panelShelf": "Panel/Shelf",
      "portBlock": "Port/Block"
    }
  }
}


Example Request
Delete Draft virtualConnect Order
curl --location --request DELETE "{{url}}/sdp/ordering/v1/api/vc/orders/5" \
  --header "Content-Type: application/json" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --data "{
  \"id\": 13381,
  \"location\": {
    \"id\": 3289814,
    \"name\": \"ATL1\",
    \"city\": \"Atlanta\",
    \"state\": \"GA\"
  },
  \"salesforceContract\": {
    \"salesForceContractId\": \"8003B000000O2PDQA0\",
    \"number\": \"00007885\",
    \"gpNumber\": \"GPIrinaContractATL1\",
    \"location\": \"ATL1\",
    \"startDate\": \"11-09-2018\",
    \"endDate\": \"11-09-2019\"
  },
  \"expeditedOrder\": true,
  \"description\": \"Description_updated\",
  \"customerReferenceName\": \"Customer Refence Name_updated\",
  \"physicalPort\": {
    \"exchangePortTypeId\": \"GIG_E_1\",
    \"exchangePortTypeDescription\": \"1 Gig-E\",
    \"cableTypeId\": \"SMF\",
    \"cableTypeDescription\": \"SMF\",
    \"exchangeVisibility\": true,
    \"customerSide\": {
      \"connectorTypeId\": \"LC\",
      \"rackId\": \"84b0b8596fe215400247af512e3ee47c\",
      \"rackName\": \"ENC-MIA1-DC1-1-POD2-208-05-Rack QVI03\",
      \"endpointType\": \"PATCH_PANEL\",
      \"patchPanel\": \"PP name\",
      \"port\": \"Port\",
      \"comment\": \"Comments\",
      \"destinationType\": \"CONNECT_TO_MY_CAGE\",
      \"panelShelf\": \"Panel/Shelf\",
      \"portBlock\": \"Port/Block\"
    }
  }
}"
Remote Hands

GET Get List of Subsriptions
{{url}}/sdp/ordering/v1/api/rhe/subscriptions
Returns full list of Remote Hands Subscriptions for your environment(s).

Filters supported:

month - optional parameter, YYYY-MM format is expected. Limits results to only those subscriptions that were active during selected month.
location - optional parameter, data center code is expected, limits results to subscriptions related to specified data center only.
Search filters must be specified once, separated by &, search parameters specified after ?

Example: GET /sdp/ordering/v1/api/rhe/subscriptions/{subscription ID} - returns information about specific Remote Hands subscription.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
 curl --location --request GET "{{url}}/sdp/ordering/v1/api/rhe/subscriptions" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "pageInfo": {
    "pageSize": 20,
    "totalCount": 1,
    "totalPages": 1,
    "pageNumber": 1
  },
  "data": [
    {
      "id": 206,
      "number": "Order_RHE_000000072",
GET Get List of One Time Requests
{{url}}/sdp/ordering/v1/api/rhe/subscriptions
Returns list of One-Time Remote Hands requests that are not related to any of 'Fulfilled' Remote Hands subscription.

Filters supported:

month - optional paarameter, YYYY-MM format is expected. Limits results set to only those requests that have time spent during selected month.
location - optional parameter, data center code is expected, limits results to requests related to specified data center only.
Search filters must be specified once, separated by &, search parameters specified after ?

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get list of One Time Requests
curl --location --request GET "{{url}}/sdp/ordering/v1/api/rhe/subscriptions" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "pageInfo": {
    "pageSize": 20,
    "totalCount": 1,
    "totalPages": 1,
    "pageNumber": 1
  },
  "data": [
    {
      "id": 206,
      "number": "Order_RHE_000000072",
GET Get Remote Hands Products & Location-Specific Prices
{{url}}/sdp/ordering/v1/api/rhe/products
Returns available products for Remote Hands service and location-specific price per hour of the service.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get Remote Hands products and Location-specific prices
curl --location --request GET "{{url}}/sdp/ordering/v1/api/rhe/products" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
[
  {
    "id": 1,
    "name": "RH w-Monthly Commit MRC",
    "prices": [
      {
        "id": 19,
        "location": "RIC1",
        "price": 100
      },
      {
POST Create New Draft Remote Hands Subcription
{{url}}/sdp/ordering/v1/api/rhe/subscriptions
Create new 'Draft' Remote Hands subcription. Newly created subcription details would be returned as response

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
	"orderProduct":{
		"product":{
			"id":25,
			"location":"MIA1",
			"price":100
		},
			"quantity":5
	},
			"environment":"kim_demo_environment",
			"location":{
				"id": 3289737, 
				"name": "MIA1", 
				"city": null, 
				"state": null
				
			},
	"description":"Comments for demonstration sunscription",
	"contactPerson":{
		"name":"Contact person Name here",
		"phone":"123456789"
	},
	"salesforceContract": {
       	"salesForceContractId": null
   	}
}



Example Request
Create New Draft Remote Hands Subcription
curl --location --request POST "{{url}}/sdp/ordering/v1/api/rhe/subscriptions" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
	\"orderProduct\":{
		\"product\":{
			\"id\":25,
			\"location\":\"MIA1\",
			\"price\":100
		},
			\"quantity\":5
	},
			\"environment\":\"kim_demo_environment\",
			\"location\":{
				\"id\": 3289737, 
				\"name\": \"MIA1\", 
				\"city\": null, 
				\"state\": null
				
			},
	\"description\":\"Comments for demonstration sunscription\",
	\"contactPerson\":{
		\"name\":\"Contact person Name here\",
		\"phone\":\"123456789\"
	},
	\"salesforceContract\": {
       	\"salesForceContractId\": null
   	}
}

"
POST Submit Existing Draft Remote Hands Subscription
{{url}}/sdp/ordering/v1/api/rhe/subscriptions/<subscription id>/submit
Updates existing 'Draft' Remote Hands subcription and submit it. Subscription ID must be specified in URL.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
    "id": 13748,
    "number": "Order_RHE_000001638",
    "created": "2019-03-21T13:42:53.004 +0000",
    "location": {
        "id": 3289737,
        "name": "MIA1",
        "city": "Miami",
        "state": "FL"
    },
    "description": "Comments for demonstration sunscription",
    "contactPerson": {
        "id": 10607,
        "name": "Contact person Name here",
        "phone": "123456789"
    },
    "environment": "kim_demo_environment",
    "orderProduct": {
        "product": {
            "id": 25,
            "location": "MIA1",
            "price": 100
        },
        "quantity": 5
    },
    "status": {
        "value": "DRAFT"
    }
}


Example Request
Submit Existing Draft Remote Hands Subscription
curl --location --request POST "{{url}}/sdp/ordering/v1/api/rhe/subscriptions/%3Csubscription%20id%3E/submit" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
    \"id\": 13748,
    \"number\": \"Order_RHE_000001638\",
    \"created\": \"2019-03-21T13:42:53.004 +0000\",
    \"location\": {
        \"id\": 3289737,
        \"name\": \"MIA1\",
        \"city\": \"Miami\",
        \"state\": \"FL\"
    },
    \"description\": \"Comments for demonstration sunscription\",
    \"contactPerson\": {
        \"id\": 10607,
        \"name\": \"Contact person Name here\",
        \"phone\": \"123456789\"
    },
    \"environment\": \"kim_demo_environment\",
    \"orderProduct\": {
        \"product\": {
            \"id\": 25,
            \"location\": \"MIA1\",
            \"price\": 100
        },
        \"quantity\": 5
    },
    \"status\": {
        \"value\": \"DRAFT\"
    }
}
"
PUT Update Draft Remote Hands Subscription
{{url}}/sdp/ordering/v1/api/rhe/subscriptions/<subscription id>
Updates existing 'Draft' Remote Hands subcription. Subscription ID must be specified in URL.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
    "id": 13748,
    "number": "Order_RHE_000001638",
    "created": "2019-03-21T13:42:53.004 +0000",
    "location": {
        "id": 3289737,
        "name": "MIA1",
        "city": "Miami",
        "state": "FL"
    },
    "description": "Comments for demonstration sunscription",
    "contactPerson": {
        "id": 10607,
        "name": "Contact person Name here",
        "phone": "123456789"
    },
    "environment": "kim_demo_environment",
    "orderProduct": {
        "product": {
            "id": 25,
            "location": "MIA1",
            "price": 100
        },
        "quantity": 5
    },
    "status": {
        "value": "DRAFT"
    }
}


Example Request
Update Draft Remote Hands Subscription
curl --location --request PUT "{{url}}/sdp/ordering/v1/api/rhe/subscriptions/%3Csubscription%20id%3E" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
    \"id\": 13748,
    \"number\": \"Order_RHE_000001638\",
    \"created\": \"2019-03-21T13:42:53.004 +0000\",
    \"location\": {
        \"id\": 3289737,
        \"name\": \"MIA1\",
        \"city\": \"Miami\",
        \"state\": \"FL\"
    },
    \"description\": \"Comments for demonstration sunscription\",
    \"contactPerson\": {
        \"id\": 10607,
        \"name\": \"Contact person Name here\",
        \"phone\": \"123456789\"
    },
    \"environment\": \"kim_demo_environment\",
    \"orderProduct\": {
        \"product\": {
            \"id\": 25,
            \"location\": \"MIA1\",
            \"price\": 100
        },
        \"quantity\": 5
    },
    \"status\": {
        \"value\": \"DRAFT\"
    }
}"
DEL Delete Remote Hands subcription
{{url}}/sdp/ordering/v1/api/rhe/subscriptions/{subscription_id}
Delete 'Draft' Remote Hands subscirption with 'Draft' status.



Example Request
Delete Remote Hands subcription
curl --location --request DELETE "{{url}}/sdp/ordering/v1/api/rhe/subscriptions/%7Bsubscription_id%7D" \
  --data ""
User Management

OVERVIEW
Roster API methods enable API user to manage Roster Records in terms of create, read, update, delete user's attributes, environments, role assignments and site access.

Additionally, the Roster API is an enabler for utilizing other operations. In order to use the Power API, you will need to learn your Environment, which is provided by the List Environment or List Roster User attributes API.

Available Roster API methods are mentioned under following URL: /sdp/api/roster/v1?_wadl

The API user must have the Roster Admin role assigned to execute Roster API methods.

FILTERING AND PAGING
v1 of the Roster APIs use the following search parameter format:
?_s=field==value

Examples:
/sdp/api/roster/v1/users?_s=username==test Search for users with username that contains 'test'

/sdp/api/roster/v1/users?_s=username==john.test Search for users that have ‘john.test’ as username

/sdp/api/roster/v1/users?pageNumber=2&pageSize=10 Return only second page, 10 results per page

/sdp/api/roster/v1/users?_s=firstName==john;lastName==lee Return Roster users with first name as ‘john’ AND last name as ‘lee’

/sdp/api/roster/v1/users?_s=firstName==john,lastName==lee Return Roster users with first name as ‘john’ OR last name as ‘lee’

/sdp/api/roster/v1/users?_s=dateCreation=lt=2018-09-01 Returns only records created before September 1, 2018

/sdp/api/roster/v1/users?_s=dateCreation=gt=2018-09-01 Return only records created after September 1, 2018

/sdp/api/roster/v1/users?orderBy=username Results will be sorted by username, ascending order by default

Badging

GET Badge Holder Summary Report
{{url}}/sdp/api/roster/reports/v1/badge_summary?startDate=2019-01-01&location=DFW1&cardIssueDate=2018-08-03&cardStatus=Active
Badge Holder Summary report provides information about Badge Holders of your Company in JSON format.

Filters supported:

location (optional, data center can be specified once, if not specified - report generated for all Data Centers that are assigned to permitted Environments of the current user)
startDate (optional, format: YYYY-MM-DD)
endDate (optional, current date by default, format: YYYY-MM-DD)
startTime (optional, format: HH:MM)
endTime (optional, 23:59 by default, format: HH:MM)
cardStatus (optional, exact macth by Card Status)
cardNumber (optional, contains characted search by card number values)
cardIssueDate (optional, format: YYYY-MM-DD, returns records that have that date as card Issue date)
cardExpireDate (optional, format: YYYY-MM-DD, returns records that have that date as card Expire date)
badgeType (optional, exact match by Badge Type)
bagdeHolderName (optional, case insenstive, contains character search thourgh Badge Holders First Name, Last Name)
Search filters must be specified once, separated by &, search parameters specified after ?

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
startDate2019-01-01
locationDFW1
cardIssueDate2018-08-03
cardStatusActive

Example Request
Badge Holder Summary Report
curl --location --request GET "{{url}}/sdp/api/roster/reports/v1/badge_summary?startDate=2019-01-01&location=DFW1&cardIssueDate=2018-08-03&cardStatus=Active" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Badge Holder Summary Report (Excel format)
{{url}}/sdp/api/roster/reports/v1/badge_summary/xlsx?startDate=2018-01-01
Badge Holder Summary report provides information about Badge Holders of your Company in excel format.

Filters supported:

location (optional, data center can be specified once, if not specified - report generated for all Data Centers that are assigned to permitted Environments of the current user)
startDate (optional, format: YYYY-MM-DD)
endDate (optional, current date by default, format: YYYY-MM-DD)
startTime (optional, format: HH:MM)
endTime (optional, 23:59 by default, format: HH:MM)
cardStatus (optional, exact macth by Card Status)
cardNumber (optional, contains characted search by card number values)
cardIssueDate (optional, format: YYYY-MM-DD, returns records that have that date as card Issue date)
cardExpireDate (optional, format: YYYY-MM-DD, returns records that have that date as card Expire date)
badgeType (optional, exact match by Badge Type)
bagdeHolderName (optional, case insenstive, contains character search thourgh Badge Holders First Name, Last Name)
Search filters must be specified once, separated by &, search parameters specified after ?

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
startDate2018-01-01

Example Request
Badge Holder Summary Report (Excel format)
curl --location --request GET "{{url}}/sdp/api/roster/reports/v1/badge_summary/xlsx?startDate=2018-01-01" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Badge Holder Summary Report (csv format)
{{url}}/sdp/api/roster/reports/v1/badge_summary/csv?startDate=2018-01-01&cardStatus=Active
Badge Holder Summary report provides information about Badge Holders of your Company in csv format.

Filters supported:

location (optional, data center can be specified once, if not specified - report generated for all Data Centers that are assigned to permitted Environments of the current user)
startDate (optional, format: YYYY-MM-DD)
endDate (optional, current date by default, format: YYYY-MM-DD)
startTime (optional, format: HH:MM)
endTime (optional, 23:59 by default, format: HH:MM)
cardStatus (optional, exact macth by Card Status)
cardNumber (optional, contains characted search by card number values)
cardIssueDate (optional, format: YYYY-MM-DD, returns records that have that date as card Issue date)
cardExpireDate (optional, format: YYYY-MM-DD, returns records that have that date as card Expire date)
badgeType (optional, exact match by Badge Type)
bagdeHolderName (optional, case insenstive, contains character search thourgh Badge Holders First Name, Last Name)
Search filters must be specified once, separated by &, search parameters specified after ?

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
startDate2018-01-01
cardStatusActive

Example Request
Badge Holder Summary Report (csv format)
curl --location --request GET "{{url}}/sdp/api/roster/reports/v1/badge_summary/csv?startDate=2018-01-01&cardStatus=Active" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Event Log Report
{{url}}/sdp/api/roster/reports/v1/event_log?startDate=2019-01-01&location=DFW1&cardIssueDate=2018-08-03&cardStatus=Active
Event Log report contains records of badges/cards usage for Badge Holders of your Company in JSON format.

Filters supported:

location (optional, data center can be specified once, if not specified - report generated for all Data Centers that are assigned to permitted Environments of the current user)
startDate (optional, format: YYYY-MM-DD)
endDate (optional, current date by default, format: YYYY-MM-DD)
startTime (optional, format: HH:MM)
endTime (optional, 23:59 by default, format: HH:MM)
cardStatus (optional, exact macth by Card Status)
cardNumber (optional, contains characted search by card number values)
cardIssueDate (optional, format: YYYY-MM-DD, returns records that have that date as card Issue date)
cardExpireDate (optional, format: YYYY-MM-DD, returns records that have that date as card Expire date)
bagdeHolderName (optional, case insenstive, contains character search thourgh Badge Holders First Name, Last Name)
logDeviceDescription (optional, case insensitive, contains character search thourgh LOGDEVICE_DESCRIPTION data)
Detail (optional, case insensitive, contains character search thourgh EVENT_DEVICE_DESCRIPTION data)
Report contents:

data related to your company only
records that have event date, time more (or equal) to report start date, time AND less (or equal) to report end date, time
records that match all other filters applied
Search filters must be specified once, separated by &, search parameters specified after ?

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
startDate2019-01-01
locationDFW1
cardIssueDate2018-08-03
cardStatusActive

Example Request
Event Log Report
curl --location --request GET "{{url}}/sdp/api/roster/reports/v1/event_log?startDate=2019-01-01&location=DFW1&cardIssueDate=2018-08-03&cardStatus=Active" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Event Log Report (Excel format)
{{url}}/sdp/api/roster/reports/v1/event_log/xlsx?startDate=2018-12-21
Event Log report contains records of badges/cards usage for Badge Holders of your Company in excel format.

Filters supported:

location (optional, data center can be specified once, if not specified - report generated for all Data Centers that are assigned to permitted Environments of the current user)
startDate (optional, format: YYYY-MM-DD)
endDate (optional, current date by default, format: YYYY-MM-DD)
startTime (optional, format: HH:MM)
endTime (optional, 23:59 by default, format: HH:MM)
cardStatus (optional, exact macth by Card Status)
cardNumber (optional, contains characted search by card number values)
cardIssueDate (optional, format: YYYY-MM-DD, returns records that have that date as card Issue date)
cardExpireDate (optional, format: YYYY-MM-DD, returns records that have that date as card Expire date)
bagdeHolderName (optional, case insenstive, contains character search thourgh Badge Holders First Name, Last Name)
logDeviceDescription (optional, case insensitive, contains character search thourgh LOGDEVICE_DESCRIPTION data)
Detail (optional, case insensitive, contains character search thourgh EVENT_DEVICE_DESCRIPTION data)
Report contents:

data related to your company only
records that have event date, time more (or equal) to report start date, time AND less (or equal) to report end date, time
records that match all other filters applied
Search filters must be specified once, separated by &, search parameters specified after ?

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
startDate2018-12-21

Example Request
Event Log Report (Excel format)
curl --location --request GET "{{url}}/sdp/api/roster/reports/v1/event_log/xlsx?startDate=2018-12-21" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Event Log Report (csv format)
{{url}}/sdp/api/roster/reports/v1/event_log/csv?startDate=2018-12-21
Event Log report contains records of badges/cards usage for Badge Holders of your Company in csv format.

Filters supported:

location (optional, data center can be specified once, if not specified - report generated for all Data Centers that are assigned to permitted Environments of the current user)
startDate (optional, format: YYYY-MM-DD)
endDate (optional, current date by default, format: YYYY-MM-DD)
startTime (optional, format: HH:MM)
endTime (optional, 23:59 by default, format: HH:MM)
cardStatus (optional, exact macth by Card Status)
cardNumber (optional, contains characted search by card number values)
cardIssueDate (optional, format: YYYY-MM-DD, returns records that have that date as card Issue date)
cardExpireDate (optional, format: YYYY-MM-DD, returns records that have that date as card Expire date)
bagdeHolderName (optional, case insenstive, contains character search thourgh Badge Holders First Name, Last Name)
logDeviceDescription (optional, case insensitive, contains character search thourgh LOGDEVICE_DESCRIPTION data)
Detail (optional, case insensitive, contains character search thourgh EVENT_DEVICE_DESCRIPTION data)
Report for Customer Users contains data that corresponds to criteria below:

data related to Customer Company only
records that have event date, time more (or equal) to report start date, time AND less (or equal) to report end date, time
records that match all other filters applied
Search filters must be specified once, separated by &, search parameters specified after ?

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
startDate2018-12-21

Example Request
Event Log Report (csv format)
curl --location --request GET "{{url}}/sdp/api/roster/reports/v1/event_log/csv?startDate=2018-12-21" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Visitor Access Log Report
{{url}}/sdp/api/roster/reports/v1/visitors_access_logs?startDate=2019-01-01
Visitor Access Log Report lists visitors who accessed QTS locations during specified period of time.

Filters supported:

location (optional, data center can be specified once, if not specified - report generated for all Data Centers that are assigned to permitted Environments of the current user)
startDate (required, format: YYYY-MM-DD)
endDate (optional, current date by default, format: YYYY-MM-DD)
startTime (optional, format: HH:MM)
endTime (optional, 23:59 by default, format: HH:MM)
visitorName (optional, case insensitive 'contains' character search by Visitor First Name, Visitor Last Name)
visitorCompany (optional, case insensitive 'contains' character search by Visitor Company)
Entry time and Exit time are limited by filters applied:

entryDateTime not after EndDate AND exitDateTime not before StartDate
exitDateTime may be blank
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
startDate2019-01-01

Example Request
Visitor Access Log Report
curl --location --request GET "{{url}}/sdp/api/roster/reports/v1/visitors_access_logs?startDate=2019-01-01" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Visitor Access Log Report (Excel format)
{{url}}/sdp/api/roster/reports/v1/visitors_access_logs/xlsx?startDate=2019-01-01
Visitor Access Log Report lists visitors who accessed QTS locations during specified period of time in excel format.

Filters supported:

location (optional, data center can be specified once, if not specified - report generated for all Data Centers that are assigned to permitted Environments of the current user)
startDate (required, format: YYYY-MM-DD)
endDate (optional, current date by default, format: YYYY-MM-DD)
startTime (optional, format: HH:MM)
endTime (optional, 23:59 by default, format: HH:MM)
visitorName (optional, case insensitive 'contains' character search by Visitor First Name, Visitor Last Name)
visitorCompany (optional, case insensitive 'contains' character search by Visitor Company)
Entry time and Exit time are limited by filters applied:

entryDateTime not after EndDate AND exitDateTime not before StartDate
exitDateTime may be blank
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
startDate2019-01-01

Example Request
Visitor Access Log Report (Excel format)
curl --location --request GET "{{url}}/sdp/api/roster/reports/v1/visitors_access_logs/xlsx?startDate=2019-01-01" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
GET Visitor Access Log Report (csv format)
{{url}}/sdp/api/roster/reports/v1/visitors_access_logs/csv?startDate=2019-01-01
Visitor Access Log Report lists visitors who accessed QTS locations during specified period of time in csv format.

Filters will be available for the Roster Admin to specify criteria to generate reports:

location (optional, data center can be specified once, if not specified - report generated for all Data Centers that are assigned to permitted Environments of the current user)
startDate (required, format: YYYY-MM-DD)
endDate (optional, current date by default, format: YYYY-MM-DD)
startTime (optional, format: HH:MM)
endTime (optional, 23:59 by default, format: HH:MM)
visitorName (optional, case insensitive 'contains' character search by Visitor First Name, Visitor Last Name)
visitorCompany (optional, case insensitive 'contains' character search by Visitor Company)
Entry time and Exit time are limited by filters applied:

entryDateTime not after EndDate AND exitDateTime not before StartDate
exitDateTime may be blank
HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
startDate2019-01-01

Example Request
Visitor Access Log Report (csv format)
curl --location --request GET "{{url}}/sdp/api/roster/reports/v1/visitors_access_logs/csv?startDate=2019-01-01" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Roster

GET Get List of Environments
{{url}}/sdp/api/roster/v1/users/environments/attributes
Lists permitted environments for API User account.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get list of Permitted Environments
curl --location --request GET "{{url}}/sdp/api/roster/v1/users/environments/attributes" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "totalCount": 2,
  "pageNumber": 1,
  "pageSize": 20,
  "pageCount": 2,
  "linkedEnvironments": [
    {
      "environmentId": 975,
      "environmentName": "qts-sales-sam-comp",
      "securityLevel": "Enterprise",
      "environmentStatus": "Active"
GET Get List of Available User Roles
{{url}}/sdp/api/roster/v1/users/(username}/environment_roles
Lists roles available with specified environment. The API Users must have Roster Admin role to run Roster APIs.

Supported filters:

username - narrows results to list of Roles available to specific User based upon the environment of the specified user. Must be specified in the URL
environmentName - narrows the results to environemtn specified
roleName - narrows the results by Roles with specified name only
If Roster Admin role is not granted to API user, the below result error will be received:
{ "code": "FORBIDDEN",
"message": "No privileges [ROSTER_VIEW_ALL] for user API user" }

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
Get list of available user roles
curl --location --request GET "{{url}}/sdp/api/roster/v1/users/%7Busername%7D/environment_roles" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "totalCount": 23,
  "pageNumber": 1,
  "pageSize": 20,
  "pageCount": 20,
  "associatedRoles": [
    {
      "roleName": "Beaty Test",
      "roleDescription": "Testing for new role creation"
    },
    {
GET Get List Roster Users
{{url}}/sdp/api/roster/v1/users
This API method return list of Roster Users related to your Company and related to at least one permitted Environment.

v1 of the Roster APIs use the following search parameter format:
?_s=field==value

Filters supported:

environmentName - returns users related to specified Environment
username - returns users with specified username
firstName - returns users with specified first name
lastName - returns users with specified last name
email - returns users with specified email address
roleName - returns users having specified role assigned
Examples:

/sdp/api/roster/v1/users?_s=username==test Search for users with username that contains 'test'

/sdp/api/roster/v1/users?_s=username==john.test Search for users that have ‘john.test’ as username

/sdp/api/roster/v1/users?pageNumber=2&pageSize=10 Return only second page, 10 results per page

/sdp/api/roster/v1/users?_s=firstName==john;lastName==lee Return Roster users with first name as ‘john’ AND last name as ‘lee’

/sdp/api/roster/v1/users?_s=firstName==john,lastName==lee Return Roster users with first name as ‘john’ OR last name as ‘lee’

/sdp/api/roster/v1/users?_s=dateCreation=lt=2018-09-01 Returns only records created before September 1, 2018

/sdp/api/roster/v1/users?_s=dateCreation=gt=2018-09-01 Return only records created after September 1, 2018

/sdp/api/roster/v1/users?orderBy=username Results will be sorted by username, ascending order by default

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}

Example Request
List Roster Users
curl --location --request GET "{{url}}/sdp/api/roster/v1/users?_s=username==portal.demo" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
  "totalCount": 1,
  "pageNumber": 1,
  "pageSize": 20,
  "pageCount": 1,
  "users": [
    {
      "username": "portal.demo",
      "userType": "CUSTOMER_STANDARD",
      "userStatus": "Active",
      "userLocked": "Not Locked",
GET Get List User Site Attributes
{{url}}/sdp/api/roster/v1/users/site_attributes?pageSize=100
This API method allows API Users with access to the Roster Admin role to List site attributes associated with their environment(s).

Filters supported:

locationName - returns users having site attributes of defined location. Case-sensitive parameter
username - returns site attributes of environment for associated user
accessStartDate - returns users with site attributes of specified Start Date for associated environment
accessEndDate - returns users with site attributes of specified End Date for associated environment
floorAccess - returns users with site attributes with specified ‘floor access’ value. boolean
officeAccess - returns users with site attributes with specified ‘Office access’ value. boolean
equipmentRemoval - returns users with site attributes with specified ‘Equipment removal’ value. boolean
requestTemporaryAccess - returns users with site attributes with specified ‘Request Visitor Access’ value. boolean
active - returns users with site attributes with specified status. boolean
Ensure pageSize parameter is greater than number of records returned.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
PARAMS
pageSize100

Example Request
List User site attributes
curl --location --request GET "{{url}}/sdp/api/roster/v1/users/site_attributes" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}"
Example Response
200 － OK
{
    "totalCount": 77,
    "pageNumber": 1,
    "pageSize": 20,
    "pageCount": 20,
    "users": [
        {
           {
            "username": "ahlanetest",
            "userSiteAttributes": [
                {
GET Roster User Data Export 
{{url}}/sdp/api/roster/v1/users/export?format=csv
This API generates .csv or .xls report file with user data for all uses that are available for requester.

Supported parameters: format - Supported values: ‘csv’, ‘xls’. Required parameter

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
PARAMS
formatcsv

Example Request
Roster User data export
curl --location --request GET "{{url}}/sdp/api/roster/v1/users/export?format=csv" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json"
Example Response
200 － OK
User Status,Locked,Username,User Type,Last Name,First Name,Title,Business Phone,Mobile Phone,Home Phone,Roster Email Notifications,Ticket Requester,Email,Environments,Roles,Badge Active,Badge Inactive,Site Attribute ID,Site Name,Access Start Date,Access End Date,Floor Access,Office Access,Equipment Removal,Change Type Notification: Routine,Change Type Notification: Comprehensive,Change Type Notification: Emergency
Active,Not Locked,api_demo,API_CUSTOMER_STANDARD,,,,,,,None,True,,qts-sales-sam-training,"API Ticketing Admin, Equipment Admin, Online Ordering, Power Dashboard, Power Device, Power Location, Roster Admin",,,,,,,,,,,,

POST Create New Roster User
{{url}}/sdp/api/roster/v1/users
Method allows privileged API Users to provision a new Customer User with CUSTOMER_STANDARD type.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{
"username":"test_user", 
"userType":"CUSTOMER_STANDARD",
"authType": "Basic",
"userStatus":"Active",
"firstName":"Test",
"lastName":"User",
"middleName":"Jr",
"title":"mr",
"email":"test@noreplyqts.com",
"homePhone":"2223032512",
"mobilePhone":"79203455566",
"businessPhone":"4445566954",
"notifications":"true",
"ticketRequester":"false",
"notifierOnly":"false",
"linkedEnvironments": [
"qts-sales-sam-training"
],
"associatedRoles": [
"Roster User"
],
"userSiteAttributes":[
{
"locationName": "ATL1",
"floorAccess": "Escorted by Customer",
"officeAccess": false,
"equipmentRemoval": false,
"accessStartDate": "2017-08-08 11:42:00 +0000",
"routine": true,
"comprehensive": true,
"emergency": true,
"routineNotifications": true,
"routineNotificationsReminderHours": 24,
"comprehensiveNotificationsReminderHours": 48
}
]
}


Example Request
Create new Roster User
curl --location --request POST "{{url}}/sdp/api/roster/v1/users" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{
\"username\":\"test_user\", 
\"userType\":\"CUSTOMER_STANDARD\",
\"authType\": \"Basic\",
\"userStatus\":\"Active\",
\"firstName\":\"Test\",
\"lastName\":\"User\",
\"middleName\":\"Jr\",
\"title\":\"mr\",
\"email\":\"test@noreplyqts.com\",
\"homePhone\":\"2223032512\",
\"mobilePhone\":\"79203455566\",
\"businessPhone\":\"4445566954\",
\"notifications\":\"true\",
\"ticketRequester\":\"false\",
\"notifierOnly\":\"false\",
\"linkedEnvironments\": [
\"qts-sales-sam-training\"
],
\"associatedRoles\": [
\"Roster User\"
],
\"userSiteAttributes\":[
{
\"locationName\": \"ATL1\",
\"floorAccess\": \"Escorted by Customer\",
\"officeAccess\": false,
\"equipmentRemoval\": false,
\"accessStartDate\": \"2017-08-08 11:42:00 +0000\",
\"routine\": true,
\"comprehensive\": true,
\"emergency\": true,
\"routineNotifications\": true,
\"routineNotificationsReminderHours\": 24,
\"comprehensiveNotificationsReminderHours\": 48
}
]
}"
Example Response
－
{
  "message": "Successfully created a new Roster User test_user "
}
POST Assign Environment to User 
{{url}}/sdp/api/roster/v1/users/{username}/environments
Method allows privileged API Customer User to assign one or several permitted Environments for existing Customer Users. The API user account must have access to environment that is being assigned.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
[
"test_environment_name_1", "test_environment_name_2"
]


Example Request
Assign Environment to Customer User
curl --location --request POST "{{url}}/sdp/api/roster/v1/users/%7Busername%7D/environments" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "[
\"test_environment_name_1, test_environment_name_2
]"
Example Response
200 － OK
{
  "message": "Successfully assigned test_environment_name_1 Environment for User test.user"
}
POST Assign Role to User 
{{url}}/sdp/api/roster/v1/users/{username}/roles
Method allows privileged API Customer User to assign one or several Roles for existing Customer Users. Username must be specified in URL.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
[
"Roster User", "Roster Admin"
]


Example Request
Assign Role to Customer User
curl --location --request POST "{{url}}/sdp/api/roster/v1/users/%7Busername%7D/roles" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "[
\"Roster User\", \"Roster Admin\"
]"
Example Response
200 － OK
{
  "message": "Successfully assigned Roster User, Roster Admin Roles for User username"
}
POST Assign New Site Attribute to User 
{{url}}/sdp/api/roster/v1/users/{username}/site_attributes
Method allows privileged API Customer Users to assign Roster Site attributes on existing Customer Users

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY

[
{
"locationName": "DFW2",
"floorAccess": "Escorted by Customer",
"officeAccess": false,
"equipmentRemoval": false,
"accessStartDate": "2017-08-08 11:42:00 +0000",
"routine": true,
"comprehensive": true,
"emergency": true,
"routineNotifications": true,
"routineNotificationsReminderHours": 24,
"comprehensiveNotificationsReminderHours": 24
}
]


Example Request
Assign new Site attribute to a Roster user
curl --location --request POST "{{url}}/sdp/api/roster/v1/users/%7Busername%7D/site_attributes" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "
[
{
\"locationName\": \"ATL1\",
\"floorAccess\": \"Escorted by Customer\",
\"officeAccess\": false,
\"equipmentRemoval\": false,
\"accessStartDate\": \"2017-08-08 11:42:00 +0000\",
\"routine\": true,
\"comprehensive\": true,
\"emergency\": true,
\"routineNotifications\": true,
\"routineNotificationsReminderHours\": 24,
\"comprehensiveNotificationsReminderHours\": 24
}
]"
Example Response
200 － OK
{
  "message": "Successfully assigned a Roster Attributes for ATL1 Location(s) for User username"
}
PUT Update Roster User Details 
{{url}}/sdp/api/roster/v1/users/{username}
Method allows privileged API Customer User to update User details for existing Customer User

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
{ 
"userStatus":"Active",
"firstName":"Alex",
"lastName":"Li",
"middleName":"J_updates",
"title":"mr",
"email":"alex.li@noreplyqts.com",
"homePhone":"2223300022",
"mobilePhone":"7920455566",
"businessPhone":"4445560006",
"notifications":"true",
"ticketRequester":"false"
}


Example Request
Update Roster User Details
curl --location --request PUT "{{url}}/sdp/api/roster/v1/users/%7Busername%7D" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "{ 
\"userStatus\":\"Active\",
\"firstName\":\"Alex\",
\"lastName\":\"Li\",
\"middleName\":\"J_updates\",
\"title\":\"mr\",
\"email\":\"alex.li@noreplyqts.com\",
\"homePhone\":\"2223300022\",
\"mobilePhone\":\"7920455566\",
\"businessPhone\":\"4445560006\",
\"notifications\":\"true\",
\"ticketRequester\":\"false\"
}"
PUT Update Existing Site Attribute of User 
{{url}}/sdp/api/roster/v1/users/{username}/site_attributes
Method allows privileged API User to update User Site attributes for existing Customer User.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY
[
{
"siteAttributeID": "1246b0101310b6c0d1047e276144b09f",
"locationName": "ATL1",
"floorAccess": "Escorted by Customer",
"officeAccess": false,
"equipmentRemoval": false,
"accessStartDate": "2016-08-08 11:42:00 +0000",
"accessEndDate": "2017-08-08 11:42:00 +0000",
"routine": true,
"comprehensive": true,
"emergency": true,
"routineNotifications": false,
"routineNotificationsReminderHours": 24,
"comprehensiveNotificationsReminderHours": 24
}
]


Example Request
Update Existing Site Attribute of User
curl --location --request PUT "{{url}}/sdp/api/roster/v1/users/%7Busername%7D/site_attributes" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "[
{
\"siteAttributeID\": \"1246b0101310b6c0d1047e276144b09f\",
\"locationName\": \"ATL1\",
\"floorAccess\": \"Escorted by Customer\",
\"officeAccess\": false,
\"equipmentRemoval\": false,
\"accessStartDate\": \"2016-08-08 11:42:00 +0000\",
\"accessEndDate\": \"2017-08-08 11:42:00 +0000\",
\"routine\": true,
\"comprehensive\": true,
\"emergency\": true,
\"routineNotifications\": false,
\"routineNotificationsReminderHours\": 24,
\"comprehensiveNotificationsReminderHours\": 24
}
]"
PUT Unlock User 
{{url}}/sdp/api/roster/v1/users/{username}/unlock
Roster user that was previously locked due to number of failed login attempts would be unlocked as result of request execution. Username must be specified in URL.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
BODY

[
{
"locationName": "DFW2",
"floorAccess": "Escorted by Customer",
"officeAccess": false,
"equipmentRemoval": false,
"accessStartDate": "2017-08-08 11:42:00 +0000",
"routine": true,
"comprehensive": true,
"emergency": true,
"routineNotifications": true,
"routineNotificationsReminderHours": 24,
"comprehensiveNotificationsReminderHours": 24
}
]


Example Request
Unlock User
curl --location --request PUT "{{url}}/sdp/api/roster/v1/users/%7Busername%7D/unlock" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data "
[
{
\"locationName\": \"DFW2\",
\"floorAccess\": \"Escorted by Customer\",
\"officeAccess\": false,
\"equipmentRemoval\": false,
\"accessStartDate\": \"2017-08-08 11:42:00 +0000\",
\"routine\": true,
\"comprehensive\": true,
\"emergency\": true,
\"routineNotifications\": true,
\"routineNotificationsReminderHours\": 24,
\"comprehensiveNotificationsReminderHours\": 24
}
]"
DEL Remove Role from User 
{{url}}/sdp/api/roster/v1/users/{username}/roles/{role_name}
Method allows privileged API Customer User to remove specified Role for existing Customer User

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json

Example Request
Remove Role from User
curl --location --request DELETE "{{url}}/sdp/api/roster/v1/users/%7Busername%7D/roles/%7Brole_name%7D" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data ""
DEL Remove Environment from User 
{{url}}/sdp/api/roster/v1/users/{username}/environments/{environment_name}
Method allows privileged API User to remove specified Environment from existing Customer User. Priveleged API user can not remove an environment from itself.

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json

Example Request
Remove Environment from User
curl --location --request DELETE "{{url}}/sdp/api/roster/v1/users/%7Busername%7D/environments/%7Benvironment_name%7D" \
  --header "Authorization: Bearer {{ACCESS_TOKEN}}" \
  --header "Content-Type: application/json" \
  --data ""
DEL Remove Site Attribute from User 
{{url}}/sdp/api/roster/v1/users/{username}/site_attributes/{siteAttributeID}
Method allows privileged API Customer User to remove specified site attribute from existing Customer User

HEADERS
AuthorizationBearer {{ACCESS_TOKEN}}
Content-Typeapplication/json
