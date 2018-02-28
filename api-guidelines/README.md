# Viable API Guidelines

  

This set of guidelines is useful when developing and testing an API, but
should be used as a checklist before handing off the product to the
client.

**Contents**

-   [Use nouns but no verbs](#use-nouns-but-no-verbs)
-   [GET method and query parameters should not alter the state](#use-nouns-but-no-verbs)
-   [Use plural nouns](#use-plural-nouns)
-   [Provide filtering, sorting, field selection and paging for collections](#collections)
    -   [Filtering](#filtering)
    -   [Sorting](#sorting)
    -   [Field selection](#field-selection)
    -   [Paging](#paging)
    -   [Listing results](#listing-results)
-   [Version your API](#version-your-api)
    -   [When to change the version](#when-to-change-the-version)
-   [Handle Errors with HTTP status codes](#handle-errors-with-http-status-codes)
-   [Keep low response times](#keep-low-response-times)
-   [Run your API on standard ports](#run-your-api-on-standard-ports)
-   [Document your API](#document-your-api)
-   [Quick checklist](#quick-checklist)
  

## **Use nouns but no verbs**

For an easy understanding use this structure for every resource:

<table>
<thead>
<tr class="header">
<th align="left">Resource</th>
<th align="left">GET<br />
read</th>
<th align="left">POST<br />
create</th>
<th align="left">PUT<br />
update</th>
<th align="left">DELETE<br />
delete</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>/cars</td>
<td>Returns a list of cars</td>
<td>Create a new car</td>
<td>Bulk update of cars</td>
<td>Delete all cars</td>
</tr>
<tr class="even">
<td>/cars/711</td>
<td>Returns a specific car</td>
<td>Method not allowed (405)</td>
<td>Updates a specific car</td>
<td>Deletes a specific car</td>
</tr>
</tbody>
</table>

Do not use verbs:

    /getAllCars
    /createNewCar
    /deleteAllRedCars

**PUT vs PATCH:** PUT should be used when sending the whole entity is
required, while PATCH should be used when only certain fields are
required in the update request.

## **GET method and query parameters should not alter the state**

Use **PUT, POST** and **DELETE** methods  instead of the **GET** method to alter the state.

Do not use **GET** for state changes:

    GET /users/711?activate or
    GET /users/711/activate 

## Use plural nouns

Do not mix up singular and plural nouns. Keep it simple and use only
plural nouns for all resources.

    /cars instead of /car
    /users instead of /user
    /products instead of /product
    /settings instead of /setting

## **Provide filtering, sorting, field selection and paging for collections<a name="collections"></a>**

### **Filtering**:

Use a unique query parameter for all fields or a query language for
filtering.

    GET /cars?color=red Returns a list of red cars
    GET /cars?seats<=2 Returns a list of cars with a maximum of 2 seats

### **Sorting**:

Allow ascending and descending sorting over multiple fields.

    GET /cars?sort=-manufactorer,+model

This returns a list of cars sorted by descending manufacturers and
ascending models.

### **Field selection**

Mobile clients display just a few attributes in a list. They don’t need
all attributes of a resource. Give the API consumer the ability to
choose returned fields. This will also reduce the network traffic and
speed up the usage of the API.

    GET /cars?fields=manufacturer,model,id,color

### **Paging**

Use limit and offset. It is flexible for the user and common in leading
databases. The default should be limit=20 and offset=0

    GET /cars?offset=10&limit=5

To send the total entries back to the user use the special key "count".

### **Listing results**

The listings should have at least the "data" key containing an array of
results. Other keys aside data could be "count" (int) and "facets" to
list the available facets.

Sample listing response:

    {
      "data": [
        {
          "id": 1,
          "model": "Mazda 3" 
        },
        {
          "id": 2,
          "name": "Audi A6" 
        }
      ],
      "count": 156,
      "facets": [
        {
          "name": "make",
          "values": [
            {
              "key": "Mazda",
              "value": 10 
            },
            {
              "key": "Audi",
              "value": 20 
            }
          ]
        }
      ]
    }

## **Version your API**

Make the API Version mandatory and do not release an unversioned API.
Use a simple ordinal number and avoid dot notation such as 2.5.

We are using the url for the API versioning starting with the letter „v“

    /blog/api/v1

### When to change the version

Services MUST increment their version number in response to any breaking
API change. Adding a new field to the API is not considered a breaking
change.

Clear examples of breaking changes:

1.  Removing or renaming API routes or API parameters
2.  Changes in behavior for an existing API
3.  Changes in Error Codes

## **Handle Errors with HTTP status codes**

It is hard to work with an API that ignores error handling. Purely
returning a HTTP 500 with a stacktrace is not very helpful.

**Use HTTP status codes**

The HTTP standard provides over 70 status codes to describe the return
values. We don’t need them all, but  there should be used at least a
mount of 10.

200 – OK – Eyerything is working  
201 – OK – New resource has been created  
204 – OK – The resource was successfully deleted

400 – Bad Request – The request was invalid or cannot be served. The
exact error should be explained in the error payload. E.g. „The JSON is
not valid“

401 – Unauthorized – The request requires an user authentication  
403 – Forbidden – The server understood the request, but is refusing it
or the access is not allowed.  
404 – Not found – There is no resource behind the URI.  
418 – I'm a teapot – **The current API version is not supported anymore,
the application should be upgraded**. This is a special status that we
internally decided to use this way.   
422 – Unprocessable Entity – Should be used if the server cannot process
the enitity, e.g. if an image cannot be formatted or mandatory fields
are missing in the payload.

500 – Internal Server Error – API developers should avoid this error. If
an error occurs in the global catch blog, the stracktrace should be
logged and not returned as response.

**Use error payloads**

All exceptions should be mapped in an error payload. Here is an example
how a JSON payload should look like.

    {
      "errors": [
       {
        "userMessage": "Sorry, the requested resource does not exist",
        "internalMessage": "No car found in the database",
        "code": 34,
        "more info": "http://dev.mwaysolutions.com/blog/api/v1/errors/12345"
       }
      ]
    } 

## **Keep low response times**

API response times should respect the following upper limits (in staging
/ production environments):

Regular Read requests - 200 ms, ideally 50 ms  
Create / Update / Delete - 500 ms  
List of items (20 items) - 250 ms  
Keyword / Advanced Search - 500 ms

## **Run your API on standard ports**

Since we build web APIs, we should ensure our APIs run on port 80 (HTTP)
or/and 443 (HTTPS). This will also avoid possible firewall issues: a lot
of users have only certain standard ports open by default and it would
require a big effort for them to open non-standard ports. 

## **Document your API**

The API should be well documented with clear examples showing both the
request and the response. We should find a solution to version the
documentation in sync with the API version.

## **Quick checklist**

<table>
<thead>
<tr class="header">
<th align="left"><strong>Section</strong></th>
<th align="left"><strong>Check</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Routes</td>
<td>Uses GET, POST, PUT, DELETE</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>Uses plural nouns for manipulating entities</td>
</tr>
<tr class="odd">
<td><br />
</td>
<td>GET calls never modify state</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>API responds on default ports (80/443)</td>
</tr>
<tr class="odd">
<td>Listings</td>
<td>Uses a query language for filtering</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>Allows sorting in multiple directions using the sort parameter</td>
</tr>
<tr class="odd">
<td><br />
</td>
<td>Allows field selection through the fields parameter</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>Allows pagination using offset &amp; limit parameters</td>
</tr>
<tr class="odd">
<td><br />
</td>
<td>Sends back the total number of elements on listings</td>
</tr>
<tr class="even">
<td>Versioning</td>
<td>API is versioned</td>
</tr>
<tr class="odd">
<td>Error handling</td>
<td>Supports multiple errors</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>Supports version deprecation</td>
</tr>
<tr class="odd">
<td><br />
</td>
<td>Respects error format</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>Keeps logs of internal errors</td>
</tr>
<tr class="odd">
<td>Response times</td>
<td>All requests are under 500 ms</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>Entity reads are under 200 ms</td>
</tr>
<tr class="odd">
<td>Documentation</td>
<td>There is an API documentation</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>The documentation is linked to the API version</td>
</tr>
<tr class="odd">
<td><br />
</td>
<td>The documentation has clear examples of request / response pairs</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>The documentation is accessible by the client</td>
</tr>
<tr class="odd">
<td>Security</td>
<td>Passwords are not kept in plain text</td>
</tr>
<tr class="even">
<td><br />
</td>
<td>The API is under SSL</td>
</tr>
</tbody>
</table>

## Sources used for inspiration

-   discussions held internally
-   [10 Best Practices for Better RESTful API](https://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/)
-   [Microsoft API Guidelines](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md#9-collections)
-   [Ultimate guide to API design](https://blog.qmo.io/ultimate-guide-to-api-design/)


  
