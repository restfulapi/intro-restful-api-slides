
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







###Singleton Controller

A single controller may be used to handle all API requests

- dependent upon URL Mapping
- supports 'nested resources'

<br />

Delegates to service based on naming conventions

- resources use pluralized, hyphenated form

```http
        /course-sections/2351  -->  CourseSectionService
```

<br />

Establishes a contract for services

- Allows use of a service adapter
- Allows explicit mapping to a service (CoC)






###HTTP

<br />

Proper, consistent HTTP support <br />
in accordance with the strategy

<br />

- HTTP Methods (GET, POST, PUT, DELETE, OPTIONS)
- HTTP Status Codes
- HTTP Headers
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
- an array or representations
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






###Declarative Marshallers







###Current Status

- Production quality, feature incomplete
 - 4288 lines test code
 - 4022 lines plugin code

<br />

Releases

- 0.1.0 - current, used in Banner XE, OSD
- 0.5.0 - June, broad production use encouraged
- 0.6.0 - July, GitHub, Grails.org
- 1.0.0 - anticipate end of year, based on feedback




###Grab it here

Git Repo: ssh://git@devgit1/framework/plugins/restful-api.git

CI Server: [http://m037138.ellucian.com:8082]()

README: []()

Strategy: [http://m037138.ellucian.com:8082/job/Ellucian%20API%20Strategy%20Documentation/HTML_Report]()

JIRA: [http://jirateams.ellucian.com:8080/login.jsp]()