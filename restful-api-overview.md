### Introducing the RESTful API Grails Plugin

![rest](/rest-pic.png)

<small>www.perfmanhr.com/blog/wp-content/uploads/2011/09/rest-relaxation-reflection.gif</small>
<br />
<br />
```
Hint: Navigate 'down' to explore current topic,
      Navigate 'right' for next topic
      You may use your arrow keys...
```






###"REpresentational State Transfer" (REST)
First described by Roy Fielding (in his [dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm))

<br />

Essentially describes the way the web works

>REST is a set of principles that define how Web standards, such as HTTP and URIs, are supposed to be used (which often differs quite a bit from what many people actually do).[1](http://www.infoq.com/articles/rest-introduction)

<br /><br />

```
Navigate 'down' for some background on REST, or 'right' to continue
```




### REST is "Simple"
<br />

REST does **NOT** propose an alternative to the web</br >
_(like SOAP/WS-\*, CORBA, RMI)_

<br />

REST provides a common and consistent interface based on proper use of HTTP

<br /><br />

```
Navigate 'down' for more info on REST, or 'right' to continue
```





###Uses Standard HTTP Methods

| HTTP action /URL      |    |   Meaning                |
|:---------------------:|:--:|:------------------------:|
| GET /things           | -> | Retrieve *list* of things |
| GET /things/23        | -> | Retrieve *one* thing identified with 23 |
| POST /things          | -> | Create a new thing |
| PATCH /things/23      | -> | Update thing 23 (or create thing 23) |
| PUT /things/23        | -> | Replace thing 23 (or create thing 23) |
| DELETE /things/23     | -> | Delete thing 23 |





###Uses Standard HTTP Status Codes
| Status Code | Description |
|:-----------:|:------------|
| 200 | The request was successful. |
| 201 | A resource was successfully created.|
| 202 | Request has been received. |
| 304 | ETag matches 'If-None-Match' header - not changed |
| 400 | The request cannot be understood. |
| 401 | Authentication failed, or not authorized. |
| 404 | The requested resource could not be found. |
| 405 | The method is not supported by the resource. |





###Status Codes continued...
| Status Code | Description |
|:-----------:|:------------|
| 406 | Not Acceptable. Accept header identifies an unsupported format. |
| 409 | There is a conflict (that violates an optimistic lock). |
| 412 | A precondition failed. Used for conditional requests. |
| 415 | Unsupported Media Type. Client requested an unsupported format. |
| 417 | Expectation Failed. |
| 422 | 'Unprocessable' entity. |
| 429 | The rate limit is exceeded |
| 500 | An undisclosed server error occurred. This is a generic 'fail whale' response. |





###It's all about 'resources'

>"A resource is anything that's important enough to be referenced as a thing in itself."
Richardson and Ruby

<br />

- 'resource orientation'
 - thinking about solutions in terms of resources
 - may significantly influence internal design & architecture
 - REST principles have been shown to reduce complexity
- 'resources' often correspond to persistent domain objects
 - but they don't have to...




###And 'representations'

- 'resources' are the concepts, or entities
- 'representations' are how they are presented
  - JSON and XML are most common
  - other representations include iCal, PDF, etc.
- a resource may have many representations

<br />
When manipulating a 'resource' through the standard REST actions, it is done by using a representation of the resource




###Learning about REST

Blogs and tutorials are everywhere...
<br />
such as the [Rest API Tutorial](http://www.restapitutorial.com)
<br />
<br />
<br />
So... how come nobody does it right?




### REST fundamentals are not enough
While REST is "easy", it doesn't answer everything...

* How do I secure my API?
* Should I use JSON?  Do I need an envelope?
* What about versioning?  How?
* Conditional requests?  CORS?

<br />

Many frameworks provide support that is naive

- promote coupling to domain objects
- don't support versioning







###Our API Strategy
<br />
We've captured best practices within an [API Strategy](http://m037138.ellucian.com:8082/job/Ellucian%20API%20Strategy%20Documentation/HTML_Report) document.<br />
<small>_(This isn't our 'invention' but simply a gathering of best practices and insights from across the web.)_</small>

Provides additional guidance beyond the fundamentals

- HTTP Status codes for various scenarios
- Standard and Custom media types
- Versioning an API
- Representations (JSON and XML)
- Resources and nested resources
- Paging and filtering lists







###RESTful API Grails Plugin
['restful-api'](http://m037138.ellucian.com:8082/job/restful-api-plugin/README.html) Grails Plugin

<br />

<large>To facilitate development of<br />
**non-trivial,** versioned <br />
APIs in accordance with our <br />
[API Strategy](http://m037138.ellucian.com:8082/job/Ellucian%20API%20Strategy%20Documentation/HTML_Report)</large>






 ### Overview Topics

- Exposing a Resource
- Processing a Request
- Responding
- Testing
- Current Status






<br />
<br />
###Exposing a Resource




###Routing Requests

A single controller may be used to handle all API requests

```groovy
class UrlMappings {

  static mappings = {

    // Normal REST mappings

    "/api/$pluralizedResourceName/$id"(controller:'restfulApi') {
        action = [GET: "show", PUT: "update", DELETE: "delete"]
        parseRequest = false
    }
    "/api/$pluralizedResourceName"(controller:'restfulApi') {
        action = [GET: "list", POST: "create"]
        parseRequest = false
    }
```




###Routing Requests

We recommend explicit support for **one** level of nesting

```groovy
class UrlMappings {

  static mappings = {

    // Also add mappings to support nesting resources one-level

    "/api/$parentPluralizedResourceName/$parentId/$pluralizedResourceName/$id"(controller:'restfulApi') {
      action = [GET: "show", PUT: "update", DELETE: "delete"]
      parseRequest = false
    }

    "/api/$parentPluralizedResourceName/$parentId/$pluralizedResourceName"(controller:'restfulApi') {
      action = [GET: "list", POST: "create"]
      parseRequest = false
    }
```




###Routing Requests

And if you need to query you can also use POST<br />
_(useful for complex queries or when criteria is private data)_

```groovy
class UrlMappings {

  static mappings = {

    // Using a different URL prefix, we'll add a mapping to
    // support querying using POST

    "/qapi/$pluralizedResourceName"(controller:'restfulApi') {
      action = [GET: "list", POST: "list"]
      parseRequest = false
    }
```




###Whitelisting Resources

Configuration may be used to 'whitelist' exposed resources

```groovy
resource 'things' config {
    representation {
        mediaTypes = ['application/vnd.hedtech.v1+json',
                      'application/json']
        marshallerFramework = 'json'
        marshallers {
            jsonDomainMarshaller {
                priority = 100
            }
        }
        jsonExtractor {}
    }
}
```




###Dynamically Exposing Resources

...or you can dynamically expose all services

```groovy
anyResource {
    representation {
        mediaTypes = ["application/json"]
        marshallers {
            jsonDomainMarshaller {
                priority = 101
            }
            jsonGroovyBeanMarshaller {
                priority = 100
            }
        }
        jsonExtractor {}
    }
}
```






<br />
<br />

### Processing a Request




###Leveraging HTTP

<br />

The plugin ensures proper, consistent HTTP support <br />
in accordance with the strategy

<br />

- Support 'GET', 'POST', 'PUT', 'DELETE', 'OPTIONS' HTTP Methods
 - Note: Currently, 'PUT' support employs 'PATCH' semantics
- Use standard HTTP Headers
- Support conditional requests
- Support CORS (Cross-Origin Resource Sharing)




###Content Negotiation

Parse request body based on 'Content-Type' header

Respond with format identified in 'Accept' header

Given:

```http
        application/xml;q=0.9,application/vnd.hedtech.v0+xml;q=1.0
```

Will use:

```
        application/vnd.hedtech.v0+xml
```

<br />

- Media Type Support
 - Built-in support for JSON and XML representations
 - Can add support for others (iCal, PDF)
 - Versioning is supported through custom media types




###Content Extraction

Controller will delegate to a configured _extractor_ to process a request body into a map before passing it to a service.

```groovy
 resource 'customers' config {
     representation {
         mediaTypes = ["application/json"]
         extractor = new net.hedtech.restfulapi.CustomerExtractor()
     }
 }
```




###Content Extraction (continued...)
Three types of extractor interfaces available.  Each is responsible for returning a map of properties that the service will use to fulfill the request

* JSONExtractor - passed a JSONObject parsed by grails JSON converter
* XMLExtractor - passed a GPATHResult object parsed by grails XML converter
* RequestExtractor - passed the http request directly

<br />

Use the RequestExtractor to get access to the request body directly.  Used to support alternate data-binding frameworks (google-gson, JAXB, etc).




###Declarative Extraction
Can define rules in configuration to extract content from JSON and xml.

* Rename properties
* Provide default values
* Convert 'short-object' reference to id
* 'Flatten' maps to be compatible with grails data-binding




###Declarative Extraction (continued...)
```groovy
    resource 'purchase-orders' config {
        representation {
            mediaTypes = ["application/json"]
            jsonExtractor {
                property 'productId'      name 'productNumber'
                property 'customers.name' name 'lastName'
                property 'orderType'      defaultValue 'standard'
            }
        }
    }
````

Applied to input
```json
    {
        "productId":"123",
        "quantity":50,
        "customers":[
            {"name":"Smith"},
            {"name":"Jones"}
        ]
    }
```

Results in the map
```groovy
['productNumber':'123', 'quantity':50, 'orderType':'standard',
 customers':[['lastName':'Smith'], ['lastName':'Jones'] ]
```




###Declarative Extraction - flattening maps
Can flatten maps for compatibility with grails data binding
```groovy
    resource 'purchase-orders' config {
        representation {
            mediaTypes = ["application/json"]
            jsonExtractor {
                property 'customer' flatObject true
            }
        }
    }
```

Applied to
```json
    {"orderId":123,
     "customer": {
        "name":"Smith"
        "id":456,
        "phone-number":"555-555-5555"
     }
    }
```

Will result in the map
```groovy
['orderId':123, 'customer.name':'Smith', 'customer.id':456,
 'customer.phone-number':'555-555-5555']
 ```




###Delegating to a Service

<br />

The controller delegates to a service based on naming conventions (or configuration)

```http
    /course-sections/2351  -->  CourseSectionService
```

<br />

Establishes a contract for services

```groovy
def list(Map params)
def count(Map params)
def show(Map params)
def create(Map content, Map params)
def update(def id, Map content, Map params)
void delete(def id, Map content, Map params)
```

<small>_(but you may configure a 'ServiceAdapter' Spring bean to adapt the contract)_</small>





###Filtering Lists

Filter lists using query parameters or within a POST body
```
?filter[0][field]=description&filter[1][value]=6322&
filter[0][operator]=contains&filter[1][field]=thing&
filter[1][operator]=eq&filter[0][value]=science&max=50
```

Helper class may be used within your service to generate HQL <br />
(but it is not a sophisticated query engine)
```groovy
def query = HQLBuilder.createHQL( application, params )
def result = Thing.executeQuery( query.statement, query.parameters,
                                 params )
```
'POST' queries should use a separate URI <br />
(e.g., '/qapi' vs. '/api')




###Handling Exceptions

Built-in exception handling for:

-    OptimisticLockException
-    Validation Exception
-    UnsupportedResourceException
-    UnsupportedRequestRepresentationException
-    UnsupportedResponseRepresentationException
-    ApplicationException\*
-    AnyOtherException

<br />

\*'ApplicationException' allows your services to specify how an exception should be handled.






<br />
<br />

### Responding




###Leveraging HTTP

<br />

The plugin ensures proper, consistent HTTP support <br />
in accordance with the strategy

<br />

- Return proper HTTP Status Codes
- Use standard and custom HTTP Headers
 - 'Content-Type' specifies standard media type (to allow tools, browsers to render result)
 - A custom header (e.g., 'X-hedtech-Media-Type') identifies actual media type of response
- Include Cache Headers (ETag, Last-Modified)




###The Response Body

Response bodies contain either:

- a single representation
- an array of representations
- an empty body

<br />

'Envelope' information is contained in headers
```http
Content-Type
X-hedtech-Media-Type   <-- the 'actual' content type
X-hedtech-message      <-- localized message
X-Status-Reason        <-- Optionally returned w/ 400 response
ETag
Last-Modified
X-hedtech-totalCount   <-- paging
X-hedtech-pageOffset
X-hedtech-pageMaxSize
```




###Marshalling

<br />

* Leverages the grails converters and priority mechanism
* Ultimate flexibility
    * can write custom marshallers
    * can delegate to a custom marshalling service and use another framework (google-gson, JAXB, XMLBeans, etc)




###Declarative marshalling

* For most cases, writing a custom marshaller shouldn't be needed
* Can declare how to marshall a class in configuration
* Supports
    * including all fields except those excluded
    * including only explicity listed fields
    * renaming fields in the representation
    * declaring additional fields to include in the representation, such as affordances
    * declaring how to represent persistent associations




###Example of declaring what fields to include
```groovy
resource 'customers' config {
    representation {
        mediaTypes = ["application/json"]
        marshallers {
            jsonDomainMarshaller {
                includesFields {
                    field 'firstName'
                    field 'lastName'
                    field 'customerNo' name 'customerID'
                }
            }
        }
    }
}
```
```json
{"id":74,"version":0,"firstName":"John",
 "lastName":"Smith","customerID":"12345"}
```




###Declarative affordances

* Can inject affordances or other computed fields into representations declaratively, in a re-usable fashion (using template)
```groovy
        jsonDomainMarshallerTemplates {
            template 'domainAffordance' config {
                additionalFields { map ->
                    map['json'].property("_href",
                        "/${map['resourceName']}/${map['resourceId']}" )
                }
            }
        }
```




###Declarative affordances (continued...)
```groovy
resource 'customers' config {
    representation {
        mediaTypes = ["application/json"]
        marshallers {
            jsonDomainMarshaller {
                inherits = ['domainAffordance']
                includesFields {
                    field 'firstName'
                    field 'lastName'
                    field 'customerNo' name 'customerID'
                }
            }
        }
    }
}
```
```json
{"id":74,"version":0,"firstName":"John","lastName":"Smith",
 "customerID":"12345","_href":"/customers/74"}
```




###Deep vs Shallow Marshalling of associations
* declarative domain marshallers support both deep or 'short-object' marshalling
```groovy
resource 'purchase-orders' config {
    representation {
        mediaTypes = ["application/json"]
        marshallers {
            jsonDomainMarshaller {
                includesFields {
                    field 'poNumber'
                    field 'customer' deep false
                }
            }
        }
    }
}```
```json
{"id":74,
 "version":0,
 "poNumber":12345,
 "customer":"/customers/123"
}```




###Deep vs Shallow Marshalling (continued...)
```groovy
resource 'purchase-orders' config {
    representation {
        mediaTypes = ["application/json"]
        marshallers {
            jsonDomainMarshaller {
                supports PurchaseOrder
                includesFields {
                    field 'poNumber'
                    field 'customer' deep true
                }
            }
            jsonDomainMarshaller {
                supports Customer
                includesVersion false
                includesFields {
                    field 'lastName'
                    field 'firstName'
                    field 'phone'
                }
            }
        }
    }
}```
```json
{"id":74,
 "version":0,
 "poNumber":12345,
 "customer": {
        "id":123
        "lastName":"Smith"
        "firstName":"John"
        "phone":"555-555-5555"
    }
}```




###Deep vs Shallow Marshalling (continued...)
* default format for a 'short-object' can be changed in configuration
* can apply to all representations via template inheritance




###Reusing marshallers
* Marshallers that need to be reused (such as a deep-marshalled sub-object) can be declarared in re-usable groups
```groovy
marshallerGroups {
    group 'customer' marshallers {
        jsonDomainMarshaller {
            supports Customer
            includesFields {
                field 'lastName'
                field 'firstName'
                field 'phone'
            }
        }
    }
}```




###Reusing marshallers (continued...)
```groovy
resource 'purchase-orders' config {
    representation {
        mediaTypes = ["application/json"]
        marshallers {
            jsonDomainMarshaller {
                supports PurchaseOrder
                includesFields {
                    field 'poNumber'
                    field 'customer' deep true
                }
            }
            marshallerGroup 'customer'
        }
    }
}```





###Declarative support for both domain and POGOs
* jsonDomainMarshaller
* jsonGroovyBeanMarshaller
* xmlDomainMarshaller
* xmlGroovyBeanMarshaller

(Support for POJOs coming in the future, if needed.)




###Custom marshalling service
* Can mix marshalling frameworks in the same application - use grails converters for some objects, custom service(s) for others
* A given representation must marshall all objects (and subobjects) using a single framework
* Any bean implementing a marshalObject method can be used

```groovy
Object marshalObject(Object o, RepresentationConfig config)
```




###Custom marshalling service (continued...)
* marshalObject can return any of the following:
    * String
    * byte[]
    * InputStream
    * StreamWrapper (used to convey both an InputStream and the value of the Content-Length header)
* Allows for marshalling to binary representations (pdf, compression of large results downloaded as a file)




###Custom marshalling service - iCalendar example
Use ical4j library to create and marshall calendars
```groovy
import net.fortuna.ical4j.model.*
import net.fortuna.ical4j.model.property.*
class CalendarService{
    def show( Map params ) {
        def builder = new ContentBuilder()
        def calendar = builder.calendar() {
            prodid('-//John Smith//iCal4j 1.0//EN')
            version('2.0')
            vevent() {
                uid('1')
                dtstamp(new DtStamp())
                dtstart('20090810', parameters: parameters() {
                    value('DATE')})
                action('DISPLAY')
                attach('http://example.com/attachment', parameters: parameters() {
                    value('URI')})
            }
        }
        calendar.validate()

        calendar
    }
}
```




###Custom marshalling service - iCalendar example (continued...)
```groovy
import groovy.xml.MarkupBuilder
import net.hedtech.restfulapi.config.RepresentationConfig

/**
 * A demonstration class for custom marshalling of iCalendar objects.
 * In this case, we are using ical4j, so we only need to invoke
 * toString on the passed objects.
 */
class ICalendarMarshallingService {

    @Override
    String marshalObject(Object o, RepresentationConfig config) {
        if (!(o instanceof net.fortuna.ical4j.model.Calendar)) {
            throw new Exception("Cannot marshal instances of" + o.getClass().getName())
        }
        return o.toString()
    }
}```





###Custom marshalling service - iCalendar example (continued...)
Define the calendar resource and representation using the custom marshalling service
```groovy
resource 'calendars' config {
    methods = ['show']
    representation {
        mediaTypes = ['text/calendar']
        contentType = 'text/calendar'
        marshallerFramework = 'ICalendarMarshallingService'
    }
}
```




###Custom marshalling service - iCalendar example (continued...)
```
curl -i --noproxy localhost -H "Accept: text/calendar" http://localhost:8080/test-restful-api/api/calendars/1
```

```
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
ETag: c44e6bb0-703f-4b39-9f5c-627aedbebc71
Last-Modified: Wed, 24 Jul 2013 15:04:32 GMT
X-hedtech-Media-Type: text/calendar
X-hedtech-message: Details for the calendar resource
Content-Type: text/calendar;charset=utf-8
Transfer-Encoding: chunked
Date: Wed, 24 Jul 2013 15:04:32 GMT

BEGIN:VCALENDAR
PRODID:-//John Smith//iCal4j 1.0//EN
VERSION:2.0
BEGIN:VEVENT
UID:1
DTSTAMP:20130724T150432Z
DTSTART;VALUE=DATE:20090810
ACTION:DISPLAY
ATTACH;VALUE=URI:http://example.com/attachment
END:VEVENT
END:VCALENDAR
```







###Testing

- Significant test code
 - 623 automated tests at time of 0.5.0 release
 - 4288 lines test code
 - 4022 lines plugin code

- Written in Spock (BDD framework)

<br />

***Plugin includes abstract Spock spec class to streamline functional testing of REST APIs***







###Current Status

- Production quality, 0.5.0 release July 12, 2013

<br />

Releases

- 0.1.0 - early-access, used in Banner XE
- 0.5.0 - current, broad production use encouraged
- 0.6.0 - targeted for Aug, GitHub, Grails.org
- 1.0.0 - anticipate end of year, based on feedback




###Grab it here

Git Repo: <br />
ssh://git@devgit1/framework/plugins/restful-api.git

CI Server: <br />
[http://m037138.ellucian.com:8082]()

README: <br />
[http://m037138.ellucian.com:8082/job/restful-api-plugin/README.html]()

Strategy: <br />
[http://m037138.ellucian.com:8082/job/Ellucian%20API%20Strategy%20Documentation/HTML_Report]()

JIRA: <br />
[http://jirateams.ellucian.com:8080/login.jsp]()

iCalendar example: <br />
[http://m037138.ellucian.com:8082/job/restful-api-plugin/HOWTO-iCalendar.html]()