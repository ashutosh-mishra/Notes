# OData(Open Data Protocol)

Odata is a standard protocol for creating & consuming APIs. OData is data-access protocol, built on HTTP, REST framework, to define best practices for building and consuming RESTful APIs.

OData is based on Entity Data Model(EDM). The EDM describes the data exposed by OData provider through metadata containing no of meta entities. This includes:
- Entity types, its properties and keys
- Entity sets, aggregated entities of a given type
- Relationship between entities
- Complex types, nested structures embedded in entities

Protocol supports the desciption of data models, the entity & the querying of data according to models.

OData follows following principles:
- Mechinism that work on variety of data sources, not assuming relational model
- Follows REST principles
- Extensibility, to support adding new features without breaking existing clients



### Terminology

**URL Components**
```
<Service root URL>/<Resource Path>?<Query options>
```

- Service root URL - Root of OData service, terminates with forward slash
- resource path    - Resource path
- query options    - Qeury options, to apply on results

For Ex: https://host:port/path/servicePath/Categories/Products?top=10&orderBy=time


- Metadata   - Description of data model
- Data       - Set of data entities & relation betwene them
- Querying   - requesting that service performs a set of filtering & transformations to data before returning result
- Entity     - 
- Operations - Invoking custome logic
- Vocabulary - Invoking custom semantics


Features:
- OData allows for SQL like querying capabilities
- Supports batch requests
- Uses HATEOAS
- Supports data joins, relationships
- Defines a standard way to develop REST baed services, where developer focs on the App based logic instead of defining, standardizing REST APIs.



### OData vs REST

**REST**
- REST is a style, not standard
- Limited & different querying capabilities
- CRUD operators are often implemented differently

**OData**
- OData is evaluaed as a standard for REST
- OData is similar to SQL, built to query web on top of HTTP, JSON, while leveraging REST principles
- Allows reading query strings with special conditions & filters using oData documented syntax
- Uses HATEOAS
- Data joins, relationsships are supported


OData provides a standard way to describe & query both data and data model.
Issue with REST is, REST doesn't provide a standard way for defining, consuming APIs. Each companies define & develop APIs differently. OData is developed to fill this gap, and provides a standard for developing REST APIs.



### *$metadata*
- Spec mandates that *metadata* doc should be available at service root URL i.e. <serviceRoot URL>/$metadata
- *$metadata* URL should return an XML doc, containing the schemas supported by server




### _Access Data_
__*GET APIs*__

a. Requesting Resources, read an entity list      
```
<serviceRoot URL>/<entity>
```   
Returns a list of entities

&nbsp;

b. Requesting an individual resource, Read a single entity
```
<serviceRoot URL>/<entity>('Entity Name')
```

b.1 Get a property from selected entity
```
<serviceRoot URL>/<entity>('Entity Name')/FirstName
```
&nbsp;

__c. Queries__

Apply set of transformations, filters before returning the result
```
<serviceRoot URL>/<entity>?$select=col1, col2, col3&$top=2
```
&nbsp;
c.1 Sorting
Sort a collection
```
<serviceRoot URL>/<entity>?$orderBy=FirstName desc
```
c.2 Pagination
```
<serviceRoot URL>/<entity>?$limit=3&$offset=10
<serviceRoot URL>/<entity>?$top=1&$skip=5
```
c.3 Return total counts
```
<serviceRoot URL>/<entity>?$count
```
c.4 Select the properties
```
<serviceRoot URL>/<entity>?$select=FirstName, LastName, Gender
```
c.5 Expand related entities



__d. Filters__

&nbsp;
d.1 Filter on property
```
<serviceRoot URL>/<entity>?$filter=FirstName eq 'Ash'
```
d.2 Filter on enum value
```
<serviceRoot URL>/<entity>?$filter=Gender eq Odata.Models.PersonGender'Female'
```
d.3 Filter on complex/nested schemas
```
<serviceRoot URL>/<entity>?$filter=Location/City/Region eq 'California'
```
d.4 Filter using logical operator
d.5 Filter any/all

&nbsp;
&nbsp;
&nbsp;

__*Create/Update/Delete APIs*__

a. Creating a new resource
```
POST <serviceRoot URL>/<entity>
```   
b. Updating a new resource
```
PUT <serviceRoot URL>/<entity>('Entity Name')
```   
c. Deleting a new resource
```
DELETE <serviceRoot URL>/<entity>('Entity Name')
```   

