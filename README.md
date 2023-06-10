# GraphQL
# Why GraphQL?
REST is an API design architecture, which, in the last few years, has become the norm for implementing web services. It uses HTTP to get data and perform various operations (POST, GET, PUT, and DELETE) in JSON format, allowing better and faster parsing of data.
However, like all great technologies, REST API comes with some downsides. Here are some of the most common ones:
- It fetches all data, whether required or not (“over-fetching”).
- It makes multiple network requests to get multiple resources.
- Sometimes resources are dependent, which causes waterfall network requests.
To overcome these, Facebook developed GraphQL, an open-source data query and manipulation language for APIs.
Benefits
# Strongly-typed Schema
All the types (such as Boolean, String, Int, Float, ID, Scalar) supported by the API are specified in the schema in GraphQL Schema Definition Language (SDL), which helps determine the data that is available and the form it exists in. This, consequently, makes GraphQL less error-prone, and more validated, and provides auto-completion for supported IDE/editors.


# No Over-Fetching or Under-Fetching
With GraphQL, developers can fetch only what is required. Nothing less, nothing more. This solves the issues that arise due to over-fetching and under-fetching.
Over-fetching happens when the response fetches more than what is required. Consider the example of a blog home page. It displays the list of all blog posts (just the title and URLs). However, to display this list, you need to fetch all the blog posts (along with body data, images, etc.) through the API, and then show only what is required, usually through UI code. This impacts the performance of your app and consumes more data, which is expensive for the user.
With GraphQL, you define the exact fields that you want to fetch (i.e., Title and URL, in this case), and it fetches the data of only these fields.
Under-fetching, on the other hand, is not fetching adequate data in a single API request. In this case, you need to make additional API requests to get related or referenced data. For instance, while displaying an individual blog post, you also need to fetch the referenced author’s entry, just so that you can display the author’s name and bio.
GraphQL handles this well. It lets you fetch all relevant data in a single query.
# Saves Time and Bandwidth
GraphQL allows making multiple resources request in a single query call, which saves a lot of time and bandwidth by reducing the number of network round trips to the server. It also helps to save waterfall network requests, where you need to resolve dependent resources on previous requests. For example, consider a blog’s homepage where you need to display multiple widgets, such as recent posts, the most popular posts, categories, and featured posts. With REST architecture, displaying these would take at least five requests, while a similar scenario using GraphQL requires a single GraphQL request. Find out more with this book.
Schema Stitching for Combining Schemas
Schema stitching allows combining multiple, different schemas into a single schema. In a microservices architecture, where each microservice handles the business logic and data for a specific domain, this is very useful. Each microservice can define its own GraphQL schema, after which you’d use schema stitching to weave them into one that is accessed by the client.


# Versioning Is Not Required
In REST architecture, developers create new versions (e.g., api.domain.com/v1/, api.domain.com/v2/) due to changes in resources or the request/response structure of the resources over time. Hence, maintaining versions is a common practice. With GraphQL, there is no need to maintain versions. The resource URL or address remains the same. You can add new fields and deprecate older fields. This approach is intuitive as the client receives a deprecation warning when querying a deprecated field.
Transform Fields and Resolve With Required Shape
A user can define an alias for fields, and each of the fields can be resolved into different values. Consider images transformation API, where a user wants to transform multiple types of images using GraphQL. The query looks like this:
```
query {
    images {
        title
        thumbnail: url(transformation: {width: 50, height: 50})
        original: url,
        low_quality: url(transformation: {quality: 50})
        file_size
        content_type
  }
}
```
Apart from these reasons, there are a few other reasons why GraphQL works well for developers.
GraphIQl Editor or GraphQL Playground where entire API design for GraphQL will be represented* from the provided schema
GraphQL Ecosystem is growing fast. For example, Gatsby, the popular static site generator uses GraphQL along with React.
It’s easy to learn and implement GraphQL.
GraphQL is not limited to server-side; it can be used for frontend as well.
# When to Use GraphQL
GraphQL works best for the following scenarios:
Apps for devices such as mobile phones, smartwatches, and IoT devices, where bandwidth usage matters.
Applications where nested data needs to be fetched in a single call.
For example, a blog or social networking platform where posts need to be fetched along with nested comments and commenters details.
Composite pattern, where application retrieves data from multiple, different storage APIs
For example, a dashboard that fetches data from multiple sources such as logging services, backends for consumption stats, third-party analytics tools to capture end-user interactions.
Proxy pattern on client side; GraphQL can be added as an abstraction on an existing API, so that each end-user can specify response structure based on their needs.
For example, clients can create a GraphQL specification according to their needs on common API provided by FireBase as a backend service.
In this article, we examined how GraphQL is transforming the way apps are managed and why it’s the technology of the future. 
Queries and Mutations
Format:
```
operationType OperationName {
	
}
```
- **operationType**: The operation type is either query, mutation, or subscription and describes what type of operation you're intending to do.
- **OperationName**: The operation type is required unless you're using the query shorthand syntax, in which case you can't supply a name or variable definitions for your operation.
The operation name is a meaningful and explicit name for your operation. It is only required in multi-operation documents, but its use is encouraged because it is very helpful for debugging and server-side logging.

```
const Book = new GraphQLObjectType({
  name: "Book",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    genre: { type: GraphQLString },
    author: {
      type: Author,
      resolve(parent, args) {
        console.log("resolving author of book=" + parent.id);
        return AuthorSchema.findById(parent.authorId);
      },
    },
  }),
});

const Author = new GraphQLObjectType({
  name: "author",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    age: {
      type: GraphQLInt,
      args: {
        format: {
          type: new GraphQLEnumType({
            name: "ageFormat",
            values: {
              DAY: { value: 0 },
              YEAR: { value: 1 },
            },
          }),
        },
      },
      resolve(parent, args) {
        return args.format === 0 ? parent.age * 365 : parent.age;
      },
    },
    books: {
      type: new GraphQLList(Book),
      resolve(parent) {
        console.log("resolving books");
        return BookSchema.find({ authorId: parent.id });
      },
    },
  }),
});

const RootQuery = new GraphQLObjectType({
  name: "RootQuery",
  fields: {
    book: {
      type: Book,
      args: { id: { type: GraphQLID } },
      resolve(parent, args) {
        return BookSchema.findById(args.id);
      },
    },
    author: {
      type: Author,
      args: { id: { type: GraphQLID } },
      resolve(parent, args) {
        console.log("resolving author");
        return AuthorSchema.findById(args.id);
      },
    },
    books: {
      type: new GraphQLList(Book),
      resolve(parent, args) {
        return BookSchema.find({});
      },
    },
    authors: {
      type: new GraphQLList(Author),
      resolve(parent, args) {
        return AuthorSchema.find({});
      },
    },
  },
});

const Mutation = new GraphQLObjectType({
  name: "Mutation",
  fields: {
    addAuthor: {
      type: Author,
      args: { name: { type: GraphQLString }, age: { type: GraphQLInt } },
      resolve(parent, args) {
        const author = new AuthorSchema({ name: args.name, age: args.age });
        return author.save();
      },
    },
    addBook: {
      type: Book,
      args: {
        name: { type: GraphQLString },
        genre: { type: GraphQLString },
        authorId: { type: GraphQLID },
      },
      resolve(parent, args) {
        const book = new BookSchema({
          name: args.name,
          genre: args.genre,
          authorId: args.authorId,
        });
        return book.save();
      },
    },
  },
});
module.exports = new GraphQLSchema({
  query: RootQuery,
  mutation: Mutation,
});
```
- query
1. syntax
```
query GetAllBooks {
  books{
    id
    name
  }
}
```
shorthand
```
{
  books{
    id
    name
  }
}
```
2. Arguments
```
author(id: "6483e5673269950fc139310b") {
    id
    name
    #supports arguments on any field
    age(format: YEAR)
  }
```
3. aliases: if we want to query for same object multiple times, then to avoid name clashing, we can use aliases
```
query AuthorList {
  #alias
  aythor1: author(id: "6483e5673269950fc139310b") {
    id
    name
    age(format: YEAR)
  }
  author2: author(id: "6483e77ac2ecabdeff427980") {
    id
    name
    ageInDay:age(format: DAY)
    age:age(format:YEAR)
  }
}
```
