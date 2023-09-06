---
tags:
  - vulnerabilty
  - "#graphql"
  - "#introspection"
  - "#mutations"
  - idors
---
# What is GraphQL

Is a query language used to manipulate data with different APIs

---

# Exploiting

**localhost:5000**

![](../../Images/Pasted%20image%2020230906221959.png)

The credentials are **johndoe** and **password1**

![](../../Images/Pasted%20image%2020230906222109.png)

You can try to fuzz the website with [Gobuster](../../Tools/Enumeration/Gobuster.md):

````bash
gobuster dir -u http://localhost:5000 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20
````

You will found a **settings**
![](../../Images/Pasted%20image%2020230906222311.png)

You can find this in "settings":

![](../../Images/Pasted%20image%2020230906222336.png)

You can intercept the request with BurpSuite and click on forward and you have to see this request
![](../../Images/Pasted%20image%2020230906222548.png)

Okay, send that to the **Repeater**

If you visit the page:

> http://localhost:5000/graphql

You see like a terminal, all the data that is sending to this are queries that obtain the result and return that.

If you see the request, they indicate that the id of the user is **1** and request more data about this user

![](../../Images/Pasted%20image%2020230906222857.png)

And in response we obtain the data

![](../../Images/Pasted%20image%2020230906222923.png)

And yes, if you are thinking about to apply IDOR in id user you are on the right track

If you change the ID and put 2 in place of 1 you see other response:

![](../../Images/Pasted%20image%2020230906223115.png)
![](../../Images/Pasted%20image%2020230906223121.png)

---

# GraphQL Introspection

Change the lab

![](../../Images/Pasted%20image%2020230906223747.png)

If you intercept the request you will see that the server are sending a request to GraphQL to obtain the data that will post on the main page

![](../../Images/Pasted%20image%2020230906223947.png)
![](../../Images/Pasted%20image%2020230906223952.png)

In this case we are going to open 

> http://localhost:5000/graphql 

And we are going to execute the query here, if you go to the bible [hacktricks](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/graphql) you can see this query that is used to **discover schema information**

````bash
query={__schema{types{name,fields{name}}}}
````

This is used in a URL in our case, we have this type of interactive console and this param is put automatically, we can submit

````bash
{__schema{types{name,fields{name}}}}
````

If you want to see this a little more beautiful you can use Prettify button

![](../../Images/Pasted%20image%2020230906224533.png)

![](../../Images/Pasted%20image%2020230906224604.png)

We are going to use [graphql-voyager](https://github.com/graphql-kit/graphql-voyager) and is not necessary to install them, we can use the [live demo](https://graphql-kit.com/graphql-voyager/), click in **Change schema**, introspection and you put all that GraphQL response to you

**Introspection** is the capacity of describe the content of their own schema

Introspection:
````bash
/?query=fragment%20FullType%20on%20Type%20{+%20%20kind+%20%20name+%20%20description+%20%20fields%20{+%20%20%20%20name+%20%20%20%20description+%20%20%20%20args%20{+%20%20%20%20%20%20...InputValue+%20%20%20%20}+%20%20%20%20type%20{+%20%20%20%20%20%20...TypeRef+%20%20%20%20}+%20%20}+%20%20inputFields%20{+%20%20%20%20...InputValue+%20%20}+%20%20interfaces%20{+%20%20%20%20...TypeRef+%20%20}+%20%20enumValues%20{+%20%20%20%20name+%20%20%20%20description+%20%20}+%20%20possibleTypes%20{+%20%20%20%20...TypeRef+%20%20}+}++fragment%20InputValue%20on%20InputValue%20{+%20%20name+%20%20description+%20%20type%20{+%20%20%20%20...TypeRef+%20%20}+%20%20defaultValue+}++fragment%20TypeRef%20on%20Type%20{+%20%20kind+%20%20name+%20%20ofType%20{+%20%20%20%20kind+%20%20%20%20name+%20%20%20%20ofType%20{+%20%20%20%20%20%20kind+%20%20%20%20%20%20name+%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}++query%20IntrospectionQuery%20{+%20%20schema%20{+%20%20%20%20queryType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20mutationType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20types%20{+%20%20%20%20%20%20...FullType+%20%20%20%20}+%20%20%20%20directives%20{+%20%20%20%20%20%20name+%20%20%20%20%20%20description+%20%20%20%20%20%20locations+%20%20%20%20%20%20args%20{+%20%20%20%20%20%20%20%20...InputValue+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}
````

You can put this in the URL:

````bash
http://localhost:5000/graphql/?query=fragment%20FullType%20on%20Type%20{+%20%20kind+%20%20name+%20%20description+%20%20fields%20{+%20%20%20%20name+%20%20%20%20description+%20%20%20%20args%20{+%20%20%20%20%20%20...InputValue+%20%20%20%20}+%20%20%20%20type%20{+%20%20%20%20%20%20...TypeRef+%20%20%20%20}+%20%20}+%20%20inputFields%20{+%20%20%20%20...InputValue+%20%20}+%20%20interfaces%20{+%20%20%20%20...TypeRef+%20%20}+%20%20enumValues%20{+%20%20%20%20name+%20%20%20%20description+%20%20}+%20%20possibleTypes%20{+%20%20%20%20...TypeRef+%20%20}+}++fragment%20InputValue%20on%20InputValue%20{+%20%20name+%20%20description+%20%20type%20{+%20%20%20%20...TypeRef+%20%20}+%20%20defaultValue+}++fragment%20TypeRef%20on%20Type%20{+%20%20kind+%20%20name+%20%20ofType%20{+%20%20%20%20kind+%20%20%20%20name+%20%20%20%20ofType%20{+%20%20%20%20%20%20kind+%20%20%20%20%20%20name+%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}++query%20IntrospectionQuery%20{+%20%20schema%20{+%20%20%20%20queryType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20mutationType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20types%20{+%20%20%20%20%20%20...FullType+%20%20%20%20}+%20%20%20%20directives%20{+%20%20%20%20%20%20name+%20%20%20%20%20%20description+%20%20%20%20%20%20locations+%20%20%20%20%20%20args%20{+%20%20%20%20%20%20%20%20...InputValue+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}
````

But there are some problems...

![](../../Images/Pasted%20image%2020230906225606.png)

We can fix them in the left part, for example "you have to put `__Type`"

And one error message is deleted

The clean code would be this:

````java
fragment FullType on __Type {
  kind
  name
  description
  fields {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues {
    name
    description
  }
  possibleTypes {
    ...TypeRef
  }
}

fragment InputValue on __InputValue {
  name
  description
  type {
    ...TypeRef
  }
  defaultValue
}

fragment TypeRef on __Type {
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
        ofType {
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
      }
    }
  }
}

query IntrospectionQuery {
  __schema {
    queryType {
      name
    }
    mutationType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      description
      locations
      args {
        ...InputValue
      }
    }
  }
}

````

And now copy all the response, go to [live demo](https://graphql-kit.com/graphql-voyager/) and put the data in introspection

![](../../Images/Pasted%20image%2020230906230138.png)

Click Display button and you see the schema

![](../../Images/Pasted%20image%2020230906230206.png)

Now if you go again to Burpsuite you can indicate what type of information do you want

Request:
![](../../Images/Pasted%20image%2020230906230926.png)

Response:
![](../../Images/Pasted%20image%2020230906230931.png)

We can't write or modified nothing, **only read** to do this we need mutations

---

# GraphQL-Mutations

This is a type of operation that is used to **modified or changed** the data of the server

![](../../Images/Pasted%20image%2020230906231730.png)

You can create a new post clicking the button 

If you intercept the request when click the button you can see the query

![](../../Images/Pasted%20image%2020230906231854.png)

You can change this data
![](../../Images/Pasted%20image%2020230906232017.png)

![](../../Images/Pasted%20image%2020230906232032.png)



---

# Extra

There is a way to realize a Dos attack or sometimes you can inject SQL injections where you are trying to authenticate like an user

---

# Tools

We are going to use [graphql-voyager](https://github.com/graphql-kit/graphql-voyager)


---


# Labs

## GraphQL IDOR

- skf-labs/nodeJs/Graphql-IDOR/

`npm install --force`
`npm start`

## GraphQL-Introspection

- skf-labs/nodeJs/Graphql-Introspection/

`npm install --force`
`npm start`

## GraphQL-Mutations

- skf-labs/nodeJs/Graphql-Mutations/

`npm install --force`
`npm start`