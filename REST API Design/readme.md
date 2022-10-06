1. [Foundations](#foundations)
    1. [Characteristics](#characteristics)
    1. [Design Flow](#design-flow)
    1. [Resources](#resources)
    1. [Methods](#methods)
1. [Requests](#requests)
1. [Responses](#responses)
1. [Additional Resources](#additional-resources)

## Foundations

### Characteristics
In general, a well designed API design will have the following characteristics:

- **Easy to read and work with**: A well designed API will be easy to work with, and its resources and associated operations can quickly be memorized by developers who work with it constantly.

- **Hard to misuse**: Implementing and integrating with an API with good design will be a straightforward process, and writing incorrect code will be a less likely outcome. It has informative feedback, and doesn’t enforce strict guidelines on the API’s end consumer.

- **Complete and concise**: Finally, a complete API will make it possible for developers to make full- fledged applications against the data you expose. Completeness happens over time usually, and most API designers and developers incrementally build on top of existing APIs. It is an ideal which every engineer or company with an API must strive towards.

### Design Flow
The Design Guide suggests taking the following steps when designing resource- oriented APIs (more details are covered in specific sections below):

- Determine what types of resources an API provides.
- Determine the relationships between resources.
- Decide the resource name schemes based on types and relationships.
- Decide the resource schemas.
- Attach minimum set of methods to resources.

### Resources
Resources are fundamental to the concept of REST. A resource is an object that’s important enough to be referenced in itself. A resource has data, relationships to other resources, and methods that operate against it to allow for accessing and manipulating the associated information. A group of resources is called a collection.

- A collection contains a list of resources of **the same type**. For example, a user has a collection of contacts.
- A resource has some state and zero or more sub-resources. Each sub-resource can be either a simple resource or a collection resource.

For example, Andela API has a collection of users, each user has a collection of placements, a cohort resource, a location resource and several others.

### Methods
All resources have a set of methods that can be operated against them to work with the data being exposed by the API.

REStful APIs comprise majorly of HTTP methods which have well defined and unique actions against any resource. Here’s a list of commonly used HTTP methods that define the CRUD operations for any resource or collection in a  RESTful API.

Standard Method | HTTP Method | Description
--------------- | ----------- | -----------
List | GET < collection url > | Used to retrieve a representation of  collection.
Get | GET < resource url > | Used to retrieve a representation of a resource.
Create | POST < collection url > | Used to create new resources and sub-resources.
Update | PUT or PATCH < resource url > | Used to update existing resources.
Delete | DELETE < resource url > | Used to delete existing resources.

## Requests

**Field name casing convention**

You can follow any casing convention, but make sure it is consistent across the application.

**Searching, sorting, filtering and pagination**

All of these actions are simply the query on one dataset. There will be no new set of APIs to handle these actions. We need to append the query params with the GET method API. Let’s understand with few examples how to implement these actions.

- **Sorting**: In case, the client wants to get the sorted list of users, the `GET /users` endpoint should accept multiple sort params in the query. E.g `GET /users?sort=name_asc` would sort the users by their name in ascending order.

- **Filtering**: For filtering the dataset, we can pass various options through query params.
E.g `GET /users?placement_status=available&location=Nairobi` would filter the users list data with the placement status of Available and where the location is Nairobi.

- **Searching**: When searching the user name in users list the API endpoint should be `GET /users?search=John Doe`

- **Pagination**: When the dataset is too large, we divide the data set into smaller chunks, which helps in improving the performance and is easier to handle the response. Eg. `GET /users?page=23&limit=25` means get the list of users on 23rd page, and limits the list to 25 records.

**Versioning**

When your APIs are being consumed by the world, upgrading the APIs with some breaking change would also lead to breaking the existing products or services using your APIs.

`http://api.yourservice.com/v1/companies/34/employees` is a good example, which has the version number of the API in the path. If there is any major breaking update, we can name the new set of APIs as v2 or v1.x.x

## Responses

Providing good feedback to developers on how well they are using your API goes a long way in improving adoption and retention.  Every client request and server side response is a message and, in an ideal RESTful ecosystem, these messages must be self descriptive.

Good feedback involves positive validation on correct implementation, and an informative error on incorrect implementation that can help users debug and correct the way they use the API.  HTTP status codes are bunch of standardized codes which has various explanations in various scenarios. The server should always return the right status code.

**2xx (Success category)**

These status codes represent that the requested action was received and successfully processed by the server.

- **200 Ok** The standard HTTP response representing success for GET, PUT or POST.
- **201 Created** This status code should be returned whenever the new instance is created. E.g on creating a new instance, using POST method, should always return 201 status code.
- **204 No Content** represents the request is successfully processed, but has not returned any content.

**3xx (Redirection Category)**

- **304 Not Modified** indicates that the client has the response already in its cache. And hence there is no need to transfer the same data again.

**4xx (Client Error Category)**

These status codes represent that the client has raised a faulty request.

- **400 Bad Request** indicates that the request by the client was not processed, as the server could not understand what the client is asking for.

- **401 Unauthorized** indicates that the client is not allowed to access resources, and should re-request with the required credentials.

- **403 Forbidden** indicates that the request is valid and the client is authenticated, but the client is not allowed access the page or resource for any reason. E.g sometimes the authorized client is not allowed to access the directory on the server.

- **404 Not Found** indicates that the requested resource is not available now.

- **410 Gone** indicates that the requested resource is no longer available which has been intentionally moved.

**5xx (Server Error Category)**

- **500 Internal Server Error** indicates that the request is valid, but the server is totally confused and the server is asked to serve some unexpected condition.

- **503 Service Unavailable** indicates that the server is down or unavailable to receive and process the request. Mostly if the server is undergoing maintenance.

## Additional Resources
- [Google's API Design Guide](https://cloud.google.com/apis/design/)
- [Best Practices in API Design](https://swaggerhub.com/blog/api-design/api-design-best-practices/)
- [API Design Guidelines](https://hackernoon.com/restful-api-designing-guidelines-the-best-practices-60e1d954e7c9)
