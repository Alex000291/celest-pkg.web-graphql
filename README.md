# web-graphql

Protocol status: **prototype**. The current subset is verified against GraphQL.js over HTTP and `graphql-ws` over WebSocket; broader validation, null-propagation, and HTTP specification coverage remain release gaps.

GraphQL query execution with native parsing, variables, aliases, nested selections, fragments, `@skip` / `@include`, persisted documents, bounded complexity, and partial resolver errors.

```text
cpm install web-graphql@1.0.0
```

```celest
import "web-graphql" as graphql;
byte[8] schema = graphql.schema();
schema.query("health", function(parent, arguments, context, info) {
    return "ok";
});
byte[8] document = graphql.parse("{ health }");
byte[8] result = graphql.execute(schema, document, new {});
graphql.close(document);
```

Use `limited` for query budgets and `close` when a native parsed document is no longer needed.

Wrap Task-returning resolvers with `asyncResolver` and execute them through `executeAsync` or `handlerAsync`. Synchronous resolvers can be mixed into the same asynchronous selection tree. Subscription sessions support repeated async events and bounded in-flight delivery through `subscriptionBackpressure` and `subscriptionConsume`.

`parse` and `parseOperation` return owned native documents. The caller must pass each document to `close`, including error paths. `validate` returns `{ valid, count, errors }`; resolver failures are represented in the GraphQL `errors` array while successful sibling fields remain in `data`. `handler` implements JSON-over-HTTP request parsing, validation, execution, and status mapping. Subscription sessions implement the `graphql-transport-ws` initialization, subscribe, next, and complete lifecycle.

The parser enforces native token, depth, and document-size limits. `limited` adds an application-defined complexity budget. Resolver maps and context objects are borrowed for the duration of execution; returned JSON values and response envelopes are owned values.
