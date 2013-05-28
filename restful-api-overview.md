
<br /><br />

### Restful API Plugin






<br />

- Background

- Key Features
 - Controller
 - HTTP
 - Content Negotiation
 - Declarative Marshallers

- Current Status







###Background
<br />
[Ellucian API Strategy]() approved January 2013

<br />

'Core Architecture' team started development of 'restful-api' plugin end of January
(developed by Shane & Charlie)

<br />

PURPOSE: To facilitate exposing RESTful endpoints that comply with the API strategy, within Grails projects like Banner XE







###RESTful API Controller

A single controller may be used to handle all API requests

- dependent upon URL Mapping
- supports 'nested resources'
- resources use pluralized, hyphenated form

<br />

Configuration used to 'whitelist' exposed resources

- may specify supported methods




###RESTful API Controller (continued...)

<br />

Delegates to service based on naming conventions

- may explicitly configure to override
```http
    /course-sections/2351  -->  CourseSectionService
```
- Establishes a contract for services
```groovy
def list(service, Map params)
def count(service, Map params)
def show(service, Map params)
def create(service, Map content, Map params)
def update(service, def id, Map content, Map params)
void delete(service, def id, Map content, Map params)     .
```
- may configure an 'adapter' Spring bean




###RESTful API Controller (continued...)

Exception handling:

-    OptimisticLockException
-    Validation Exception
-    UnsupportedRequestRepresentationException
-    UnsupportedResponseRepresentationException
-    ApplicationException
-    AnyOtherException

<br />

'ApplicationException' gives responsiblility to services <br />
(same contract as banner-core 'ApplicationException')






###HTTP

<br />

Proper, consistent HTTP support <br />
in accordance with the strategy

<br />

- HTTP Methods (GET, POST, PUT, DELETE, OPTIONS)
- HTTP Status Codes
- HTTP Headers (standard and custom)
- Cache Headers (ETag, Last-Modified support)
- CORS (Cross-Origin Resource Sharing) support






###Content Negotiation

Given:
```http
        application/xml;q=0.9,application/vnd.hedtech.v0+xml;q=1.0
```
Will use:
```
        application/vnd.hedtech.v0+xml
```

<br />

- Media Types
 - Supports JSON and XML representations
 - Supports custom media types
 - Supports versioning through custom media types







###Responses

Response bodies contain

- a single representation
- an array of representations
- an empty body

<br />

Envelope information contained in headers
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




###Responses (continued)

Filtering lists via query params
```
?filter[0][field]=description&filter[1][value]=6322&
filter[0][operator]=contains&filter[1][field]=thing&
filter[1][operator]=eq&filter[0][value]=science&max=50
```

Helper class may be used to generate HQL <br />
(but it is not a sophisticated query engine)
```groovy
def query = HQLBuilder.createHQL( application, params )
def result = Thing.executeQuery( query.statement, query.parameters,
                                 params )
```






###Marshalling

<br />

* Leverages the grails converters and priority mechanism
* Creates a unique named configuration for each resource/representation combination
* Ultimate flexibility: can write custom marshallers




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
{"id":74,"version":0,"firstName":"John","lastName":"Smith","customerID":"12345"}
```




###Declarative affordances

* Can inject affordances or other computed fields into representations declaratively, in a re-usable fashion
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
        inherits = ['domainAffordance']
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
{"id":74,"version":0,"firstName":"John","lastName":"Smith",
 "customerID":"12345","_href":"/customers/74"}
```






###Current Status

- Production quality, feature incomplete
 - 4288 lines test code
 - 4022 lines plugin code

<br />

Releases

- 0.1.0 - current, used in Banner XE, OSD
- 0.5.0 - July, broad production use encouraged
- 0.6.0 - July/Aug, GitHub, Grails.org
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