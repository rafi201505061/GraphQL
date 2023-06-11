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
# Queries and Mutations
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
4. Fragments: To avoid repitattive work
```
query AuthorList {
  #alias
  aythor1: author(id: "6483e5673269950fc139310b") {
    ...SameProps
  }
  author2: author(id: "6483e77ac2ecabdeff427980") {
    ...SameProps
    ageInDay:age(format: DAY)
    
  }
}

fragment SameProps on author{
 		id
    name
    age(format: YEAR)
}
```
5. variables: By using variables, we can simply pass a different variable rather than needing to construct an entirely new query.
```
query AuthorList($id:ID) {
   author(id: $id) {
    id
    name
    age(format: YEAR)
  }
}
```
- definition: 
```
$var_name: type_of_variable
```
- required or not required: 
```
$var_name: type_of_variable! (! after type_of_variable indicates thar $var_name is required, can not be null)
```
- default value: 
```
$var_name: type_of_variable = default_value
```
6. Directives: Directives are used to conditionally include or skip some fields or fragements in query.
There are two types of directives to manipulate the query,
 - > *** @include(if: Boolean) *** Only include this field in the result if the argument is true.
 - > *** @skip(if: Boolean) *** Skip this field if the argument is true.

```
query AuthorList($includeBooks: Boolean!) {
  #alias
  aythor1: author(id: "6483e5673269950fc139310b") {
    ...SameProps
    books @include (if: $includeBooks){
      id
    }
  }
}
```
- mutation
> While query fields are executed in parallel, mutation fields run in series, one after the other.
This means that if we send two incrementCredits mutations in one request, the first is guaranteed to finish before the second begins, ensuring that we don't end up with a race condition with ourselves.
```
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
    }
  ```
  ```
  mutation addBook($name:String,$genre:String,$authorId:ID){
  addBook(name:$name,genre:$genre,authorId:$authorId){
    id
  }
}
```
> It adds a book and returns the id of the newly created book
- inline fragments: If you are querying a field that returns an interface or a union type, you will need to use inline fragments to access data on the underlying concrete type.
```
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    ... on Droid {
      primaryFunction
    }
    ... on Human {
      height
    }
  }
}
```
In this query, the hero field returns the type Character, which might be either a Human or a Droid depending on the episode argument. In the direct selection, you can only ask for fields that exist on the Character interface, such as name.

To ask for a field on the concrete type, you need to use an inline fragment with a type condition. Because the first fragment is labeled as ... on Droid, the primaryFunction field will only be executed if the Character returned from hero is of the Droid type. Similarly for the height field for the Human type.

Named fragments can also be used in the same way, since a named fragment always has a type attached.

- meta fields: Given that there are some situations where you don't know what type you'll get back from the GraphQL service, you need some way to determine how to handle that data on the client. GraphQL allows you to request __typename, a meta field, at any point in a query to get the name of the object type at that point.
```
{
  search(text: "an") {
    __typename
    ... on Human {
      name
    }
    ... on Droid {
      name
    }
    ... on Starship {
      name
    }
  }
}
```
result: 
```
{
  "data": {
    "search": [
      {
        "__typename": "Human",
        "name": "Han Solo"
      },
      {
        "__typename": "Human",
        "name": "Leia Organa"
      },
      {
        "__typename": "Starship",
        "name": "TIE Advanced x1"
      }
    ]
  }
}
```
# Schema: Most types in your schema will just be normal object types, but there are two types that are special within a schema 
> i)query ii)mutation
**Every GraphQL service has a query type and may or may not have a mutation type. These types are the same as a regular object type, but they are special because they define the entry point of every GraphQL query**
# Types
- scalar types: INT, FLOAT, STRING, BOOLEAN, ID, ENUM
- Object type : {}
- List: []
- non null: !

# Interfaces
```
interface Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
}
type Human implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  starships: [Starship]
  totalCredits: Int
}

type Droid implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  primaryFunction: String
}
```
Following query produces an error,
```
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    primaryFunction
  }
}
```
> The hero field returns the type Character, which means it might be either a Human or a Droid depending on the episode argument. In the query above, you can only ask for fields that exist on the Character interface, which doesn't include primaryFunction.

To ask for a field on a specific object type, you need to use an inline fragment:
```
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    ... on Droid {
      primaryFunction
    }
  }
}
```
# Union Types
```
union SearchResult = Human | Droid | Starship
```
> Note that members of a union type need to be concrete object types; you can't create a union type out of interfaces or other unions.
In this case, if you query a field that returns the SearchResult union type, you need to use an inline fragment to be able to query any fields at all:
```
{
  search(text: "an") {
    __typename
    ... on Human {
      name
      height
    }
    ... on Droid {
      name
      primaryFunction
    }
    ... on Starship {
      name
      length
    }
  }
}
Result: 
{
  "data": {
    "search": [
      {
        "__typename": "Human",
        "name": "Han Solo",
        "height": 1.8
      },
      {
        "__typename": "Human",
        "name": "Leia Organa",
        "height": 1.5
      },
      {
        "__typename": "Starship",
        "name": "TIE Advanced x1",
        "length": 9.2
      }
    ]
  }
}
```

# Input Types
you can also easily pass complex objects. This is particularly valuable in the case of mutations, where you might want to pass in a whole object to be created. In the GraphQL schema language, input types look exactly the same as regular object types, but with the keyword input instead of type
```
input ReviewInput {
  stars: Int!
  commentary: String
}
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
  vars:
  {
  "ep": "JEDI",
  "review": {
    "stars": 5,
    "commentary": "This is a great movie!"
  }
}
output: 
{
  "data": {
    "createReview": {
      "stars": 5,
      "commentary": "This is a great movie!"
    }
  }
}
```
# Query Validations:
1. A fragment cannot refer to itself or create a cycle, as this could result in an unbounded result!
```
{
  book(id:"6483e7be5dc1e0db0c773151") {
    ...BookFragment
  }
}

fragment BookFragment on book {
  name
  genre
  author {
    books{
    	...BookFragment
    }
  }
}
```
solution:
```
{
  book(id: "6483e7be5dc1e0db0c773151") {
    ...BookFragment
    author {
      books {
        ...BookFragment
      }
    }
  }
}

fragment BookFragment on Book {
  name
  genre
}
```
2. When we query for fields, we have to query for a field that exists on the given type
```
{
  book(id:"6483e7be5dc1e0db0c773151") {
    favoriteSpaceship
  }
}
```
> it's invalid because book has no field named favoriteSpaceship
3. Whenever we query for a field and it returns something other than a scalar or an enum, we need to specify what data we want to get back from the field.
4. Similarly, if a field is a scalar, it doesn't make sense to query for additional fields on it, and doing so will make the query invalid.
5. A query can only query for fields on the type in question.

# How queries are resolved
Each field has a resolve function.
```
resolve(parent, args, context, obj)
```
- GraphQL starts resolving from the root query. 
- Fields on the same level will be resolved concurrently. Exception: mutation queries, where fields will be resolved serially from top to bottom.
- Higher level fields will get the resolved value from the immediate lower level field as **parent** in resolve functions.
- If we don't provide a resolve function, GraphQL will provide a default implementation
```
field_name: resolve(parent, args, context, obj){
return parent[field_name];
}
```



