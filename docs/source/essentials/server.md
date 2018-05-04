---
title: Building a server
---

> Estimated time: About 8 minutes.

Apollo Server provides an easy way for new, or existing, applications to get running quickly.  Existing applications can take advantage of middleware and new applications can utilize an integrated web-server.  Both of these servers can be configured with minimal configuration and follow industry best-practices.

<h2 id="installation">Installation</h2>

We need to install two packages to use Apollo Server, and a third package when using Apollo Server as middleware in an existing application:

* [`apollo-server`](//npm.im/apollo-server): The Apollo Server package, which provides most of the functionality.
* [`graphql`](//npm.im/graphql): A support library, provided by Facebook.  It won't be explicitly used in these examples, but is a required module and shared amongst all GraphQL libraries in the project.

To install, run:

    npm install --save apollo-server@beta graphql

When adding Apollo Server to an existing application, a corresponding HTTP server support package needs to be installed as well.  For example, for Express this is:

    npm install --save apollo-server-express@beta

> Note: During the beta, it's necessary to use the `beta` npm package, as shown in the above commands.

<h2 id="creating">Creating a server</h2>

The fastest way to get started with GraphQL is by creating a new server. Apollo Server will set an Express server up for you as long as you provide it with `typeDefs`, which is a string representing your GraphQL schema, and `resolvers`, which is a map of functions that implement your schema.

In the following examples, we'll import two things from `apollo-server`:

* The `ApolloServer` class, which we'll use to instantiate and start the server.
* The `gql` template literal tag, used for writing GraphQL within JavaScript code.

```js
const { ApolloServer, gql } = require('apollo-server');

// The GraphQL schema
const typeDefs = gql`
  type Query {
    "A simple type for getting started!"
    hello: String
  }
`;

// A map of functions which return data for the schema.
const resolvers = {
  Query: {
    hello: () => 'world'
  }
};

const server = new ApolloServer({
  typeDefs,
  resolvers,
});
```

> See the [API Reference](../api/apollo-server.html) for additional options which can be passed to the `ApolloServer` constructor.

<h2 id="starting">Starting a server</h2>

At this point, we're ready to start accepting connections to the server.  This is done by calling the `listen` method on the instance of `ApolloServer` which was created in the previous step:

```js
server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`)
});
```

> By default, Apollo Server listens on port 4000.  See the [API reference](../api/apollo-server.html) for additional `listen` options, including how to configure the port.

<h3 id="running">Running the server</h3>

With the above configuration complete, we can now start the Node application, with Apollo Server, for the first time.  This varies, but assuming a standard `index.js` configuration, might be as simple as `node index.js`.

After you start the server it should print a message to the console indicating that it's ready:

```shell
$ node index.js
🚀 Server ready at http://localhost:4000/
```

At this point, if the message isn't printed to the console, it's possible that something went wrong.  Double-check the previous steps in this guide, and try comparing the configuration to our [pre-configured example on Glitch]().

<h3 id="running">GraphQL Playground</h3>

To explore the newly created GraphQL server, open a browser to the link shown in the console, http://localhost:4000/graphql. Apollo Server sets up GraphQL Playground for you so that you can start running queries and exploring schemas quickly.

To run a query, copy the following query and then press the "▶️" button:

```graphql
query {
  hello
}
```

And the server should return a simple response:

```json
{
  "data": {
    "hello": "world"
  }
}
```

Your server itself is hosted at http://localhost:4000/graphql. This would be the endpoint you pass to set up Apollo Client.

<h2 id="integrations">Server integrations</h2>

Depending on whether we are creating a new application or an existing application, the steps will vary slightly since Apollo Server must adapt to the semantics of existing servers (e.g. Express, Hapi, etc.)

Both import methods will use the `apollo-server` module we installed in the previous step, but existing applications will also install a middleware package which corresponds to the desired HTTP server.

<h3 id="middleware">Middleware</h3>

Existing applications generally already have middleware in place and Apollo Server works along with those middleware.

To integrate Apollo Server with an existing server, we will need to import `registerServer` from the appropriate support package.  For example, if the application is already using an Express server, this would import `registerServer` from the `apollo-server-express` module:

```js
const { registerServer } = require('apollo-server-express');
```


For existing applications, we'll also pass the existing application into the constructor as `app`.  New applications should not pass `app`.

> The existing application is frequently already named `app`, especially when using Express.  If the application is identified by a different variable, pass the existing app in as `app`.

```js
const { ApolloServer, gql } = require('apollo-server-express');
const { typeDefs, resolvers } = require('./schema');

const server = new ApolloServer({
  // These will be defined for both new or existing servers
  typeDefs,
  resolvers,
});
```

When adding Apollo Server to an existing server, it's necessary to indicate where to activate the middleware relative to other middlewares already in the app.  To do this, add the following line along with your other middleware, preferably before any of the others:

```js
server.registerServer({ server, app });
```

<h3 id="serverless">Serverless</h3>

Apollo Server works great in "serverless" environments such as Amazon Lambda and Microsoft Azure Functions.  These implementations have some extra considerations which won't be covered in this guide.

## Next steps

Now that the GraphQL server is running, it's time to dive deeper into how we'll fetch data for our types.  We'll get started on that in the [next step](./data.html).