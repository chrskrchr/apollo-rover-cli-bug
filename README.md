Steps to reproduce the bug:

1. Clone this repo
2. Run `npm install`
3. Run `node index.js`
4. Run the following introspection query via curl:

```shell
curl 'http://localhost:4000/' \
  -H 'content-type: application/json' \
  --data-raw '{"query":"\n    query IntrospectionQuery {\n      __schema {\n        \n        queryType { name }\n        mutationType { name }\n        subscriptionType { name }\n        types {\n          ...FullType\n        }\n        directives {\n          name\n          description\n          \n          locations\n          args {\n            ...InputValue\n          }\n        }\n      }\n    }\n\n    fragment FullType on __Type {\n      kind\n      name\n      description\n      \n      fields(includeDeprecated: true) {\n        name\n        description\n        args {\n          ...InputValue\n        }\n        type {\n          ...TypeRef\n        }\n        isDeprecated\n        deprecationReason\n      }\n      inputFields {\n        ...InputValue\n      }\n      interfaces {\n        ...TypeRef\n      }\n      enumValues(includeDeprecated: true) {\n        name\n        description\n        isDeprecated\n        deprecationReason\n      }\n      possibleTypes {\n        ...TypeRef\n      }\n    }\n\n    fragment InputValue on __InputValue {\n      name\n      description\n      type { ...TypeRef }\n      defaultValue\n    }\n\n    fragment TypeRef on __Type {\n      kind\n      name\n      ofType {\n        kind\n        name\n        ofType {\n          kind\n          name\n          ofType {\n            kind\n            name\n            ofType {\n              kind\n              name\n              ofType {\n                kind\n                name\n                ofType {\n                  kind\n                  name\n                  ofType {\n                    kind\n                    name\n                  }\n                }\n              }\n            }\n          }\n        }\n      }\n    }\n  ","operationName":"IntrospectionQuery"}' \
  --compressed
```

Search for `Foo description` and note that the `Foo` enum's description is present as expected.

5. Run an introspection query via the Rover CLI: `npx -p @apollo/rover@0.3.0 rover graph introspect http://localhost:4000`

```
Introspection Response: 

"""Exposes a URL that specifies the behaviour of this scalar."""
directive @specifiedBy on SCALAR
"""Book description"""
type Book {
  """title description"""
  title: String
  """author description"""
  author: String
}
"""Query description"""
type Query {
  """books description"""
  books: [Book]
}
enum Foo {
  """BAR description"""
  BAR
}
```

Note that when the introspection is performed via Rover, the `Foo` enum's description is not included in the output.
