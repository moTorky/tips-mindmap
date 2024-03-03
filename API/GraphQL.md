after we learn about GraphQL from [[anatomy of api]], how to detect if the app use GraphQL API:
1. browser and monitor requests
2. look for cookies set by the site
3. directory fuzzing using Gobuster or Kiterunner 
   `kr brute <url> -w /usr/share/seclists/Discovery/Web-Content/graphql.txt`
```list
/graphql 
/v1/graphql 
/api/graphql 
/v1/api/graphql
/graph 
/v1/graph 
/graphiql 
/v1/graphiql 
/console 
/query 
/graphql/console 
/altair 
/playground
// repalce version number with /v2, /v3, /test, /internal, /mobile, /legacy
```
	- try to access GraphQL docs, IDE
4. after u discover the graphql endpoint, reverse engineer it by attempt every feature, capture and review request query and responses. labeling them will make it easy for you
5. obtain the schema(all the types and fields) using introspection, **introspection maybe disabled in production mode** , Testing for introspection is as simple as sending an introspection query.
> introspection query is too large so i listed it in the end of the page
6. Crafting Requests Using 
	1. the GraphiQL Documentation Explorer: GraphiQL Documentation Explorer is a tool for seeing the types, fields, and arguments available in the GraphQL documentation
	2. InQL burp extension: auto introspection
	3. [Clairvoyance](https://github.com/nikitastupin/clairvoyance/) to gain insight into a GraphQL’s structure when introspection is disabled.
	4. [ZAP GraphQL add-on](https://www.zaproxy.org/blog/2020-08-28 -introducing-the-graphql-add-on-for-zap/) auto introspection 
7. now it's time to discover some vulns such BOLA, injections, BFLA. and do some [[FUZZ]]
	1. a common misconfiguration is allowing lower-privileged users to modify a piece of data that they should not via a mutation request

#### more form GraphQL blackhat book

the following query will return all the type names in the system
```graphql
{ 
	__schema { 
		types { 
			name 
		} 
	} 
}
```
use the __type query to find the associated fields of a particular type
```graphql
{ __type(name: "customer") { name fields { name } } }
```
```graphql
query IntrospectionQuery {
    __schema {
      queryType { name }
      mutationType { name }
      subscriptionType { name }
      types {
        ...FullType
      }
      directives {
        name
        description
        args {
          ...InputValue
        }
        onOperation
        onFragment
        onField
      }
    }
  }
 
fragment FullType on \\_\\_Type {
kind
name
description
fields(includeDeprecated: true) {
name
description
args {
...InputValue
}
type {
...TypeRef
}
isDeprecated
deprecationReason
}
inputFields {
...InputValue
}
interfaces {
...TypeRef
}
enumValues(includeDeprecated: true) {
name
description
isDeprecated
deprecationReason
}
possibleTypes {
...TypeRef
}
}
 
fragment InputValue on \\_\\_InputValue {
name
description
type { ...TypeRef }
defaultValue
}
 
fragment TypeRef on \\_\\_Type {
kind
name
ofType {
kind
name
ofType {
kind
name
ofType {
kind
name
}
}
}
}
```
  