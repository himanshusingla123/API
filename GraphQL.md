GraphQL's type system is the foundation of its powerful query language, enabling precise data fetching and manipulation. It allows clients to request exactly what they need, nothing more, nothing less, and enables APIs to evolve over time without breaking existing queries. Here's an overview of the GraphQL type system with examples:

## Basic Types

#### 1. Scalar Types

These are the primitive types in GraphQL. The default scalar types include:

- **Int**: A signed 32‐bit integer.
- **Float**: A signed double-precision floating-point value.
- **String**: A UTF‐8 character sequence.
- **Boolean**: `true` or `false`.
- **ID**: A unique identifier, often used to refetch an object or as the key for a cache. The `ID` type is serialized in the same way as a `String`.

```graphql
type Character {
  name: String
  age: Int
  isHero: Boolean
}
```

#### 2. Object Types

The most common type you'll define in a GraphQL schema. They represent a kind of object you can fetch from your service, and what fields it has.

```graphql
type Character {
  name: String
  age: Int
  friends: [Character]
}
```

In this example, `Character` is an object type with `name`, `age`, and `friends` fields. `friends` is an array of `Character` objects, demonstrating how you can create relationships between types.

#### 3. Enum Types

Enums are a special kind of scalar that is restricted to a particular set of allowed values. This allows you to:

```graphql
enum Episode {
  NEWHOPE
  EMPIRE
  JEDI
}
```

This means that wherever an `Episode` is expected, only one of those three values is permitted.

### Advanced Types

#### 1. Interface

An interface is an abstract type that includes a certain set of fields that a type must include to implement the interface.

```graphql
interface Character {
  id: ID!
  name: String!
}

type Human implements Character {
  id: ID!
  name: String!
  height: Float
}

type Droid implements Character {
  id: ID!
  name: String!
  primaryFunction: String
}
```

#### 2. Union Types

A union type is similar to an interface, but it doesn't get to specify any common fields between the types.

```graphql
union SearchResult = Human | Droid | Starship

```

#### 3. Input Types

Input types are special object types that allow you to pass objects as arguments to GraphQL queries and mutations.

```graphql
input NewCharacterInput {
  name: String!
  age: Int!
}

type Mutation {
  addCharacter(input: NewCharacterInput): Character
}
```

### Query and Mutation Types

#### Query

The `Query` type is the entry point for GraphQL query operations. It's an object type that defines the set of queries that are available.

```graphql
type Query {
  characters: [Character]
  character(id: ID!): Character
}
```

#### Mutation

The `Mutation` type is the entry point for mutation operations, allowing clients to modify data.

```graphql
type Mutation {
  addCharacter(name: String!, age: Int!): Character
}
```

### Example Query and Mutation

#### Query Example

```graphql
query {
  character(id: "1000") {
    name
    age
    friends {
      name
    }
  }
}
```

#### Mutation Example

```graphql
mutation {
  addCharacter(name: "Luke Skywalker", age: 53) {
    id
    name
  }
}
```

### Conclusion

GraphQL's type system is essential for defining the capabilities of a GraphQL API. It allows the API to describe what types of data can be queried, how they're interconnected, and how they can be mutated, providing a powerful, flexible, and efficient approach to managing data in applications.
## Schema

A **Schema** defines the structure of your data and the operations (queries and mutations) that can be performed on your GraphQL server. It specifies the types of data that can be queried by the client, the relationships between these types, and how clients can interact with the data (e.g., fetching, updating).

#### Example Schema

```graphql
type Character {
  id: ID!
  name: String!
  age: Int
}

type Query {
  character(id: ID!): Character
  characters: [Character]
}

type Mutation {
  addCharacter(name: String!, age: Int!): Character
}
```

In this example, the schema defines:
- A `Character` type with fields `id`, `name`, and `age`.
- A `Query` type for fetching characters either by `id` or as a list.
- A `Mutation` type for adding a new character.

## Queries

**Queries** are operations that allow clients to fetch data from a GraphQL server. They are defined within the schema under the `Query` type and enable clients to request exactly the data they need, no more, no less.

#### Example Query

```graphql
query GetCharacter {
  character(id: "1") {
    name
    age
  }
}
```

This query requests the `name` and `age` of the character with `id` 1. It doesn't fetch `id` or any other fields that might exist on `Character` because they weren't specified in the query.

## Resolvers

**Resolvers** are the functions that fulfill the requests made by queries and mutations. Each field in a GraphQL schema is backed by a resolver that's responsible for returning data for that field. If a field is not explicitly resolved, GraphQL uses default resolvers (e.g., property access in JavaScript).

#### Example Resolvers

For the schema provided above, the resolver functions might look like this in JavaScript:

```javascript
const resolvers = {
  Query: {
    character: (parent, args, context, info) => {
      // Fetch a character by ID from your data source
      return characters.find(character => character.id === args.id);
    },
    characters: () => {
      // Return a list of characters
      return characters;
    },
  },
  Mutation: {
    addCharacter: (parent, args, context, info) => {
      const newCharacter = { id: generateId(), name: args.name, age: args.age };
      characters.push(newCharacter);
      return newCharacter;
    },
  },
};
```

In these resolvers:
- The `character` query resolver fetches a single character by its `id`.
- The `characters` query resolver returns an array of all characters.
- The `addCharacter` mutation resolver adds a new character to the list and returns it.

### Conclusion

- The **Schema** is a blueprint that defines the structure of your data and the operations available.
- **Queries** are used by clients to request data from the GraphQL server.
- **Resolvers** are functions that provide the logic to fetch the data requested by queries and mutations.

Together, these components form the backbone of any GraphQL service, allowing for flexible, efficient, and powerful data retrieval and manipulation.
