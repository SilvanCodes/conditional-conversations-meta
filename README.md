# Setup/Start Environment

1. Start development dgraph cluster. ([reference][1])
  `docker run -it -p 8080:8080 dgraph/standalone:v20.11.0`

2. Insert GQL schema by running: ([reference][2])
  `curl -X POST localhost:8080/admin/schema --data-binary '@schema.gql`
  in the folder that `schema.gql` resides in.

3. Fire up GQL-Playground via:
  `npx http-server` in the root project folder. The index.html file contains the minimum setup from [here][3]. Point the playground to `http://localhost:8080/graphql`

<!-- accessed on 2021-01-05 -->
  [1]: https://dgraph.io/docs/graphql/quick-start/#step-1---start-dgraph-graphql
<!-- accessed on 2021-01-05 -->
  [2]: https://dgraph.io/docs/graphql/quick-start/#step-2---add-a-graphql-schema
<!-- accessed on 2021-01-05 -->
  [3]: https://github.com/graphql/graphql-playground/blob/master/packages/graphql-playground-html/minimal.html


# Setup angular project

1. Install angular cli via: `npm install @angular/cli`
2. Generate project via: `npx ng new ccs-app --strict --prefix=ccs`

3. Run `npx ng add apollo-angular`

4. Run `npm i graphql`

5. Run `npm i -D @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-operations @graphql-codegen/typescript-apollo-angular`

6. Create `touch codegen.yml` with `
schema: http://localhost:8080/graphql
documents: 'src/**/*.gql'
generates:
  ./src/app/generated/gql.ts:
    plugins:
      - typescript
      - typescript-operations
      - typescript-apollo-angular
`

7. Add `{
  "scripts": {
    "generate": "graphql-codegen"
  }
}` to `package.json`

8. Install `npm i subscriptions-transport-ws @types/ws` to use GQL subscriptions.

9. To use local library do like [so][4] (npm link) and set `preserveSymlinks` like [so][5].

[4]: https://docs.npmjs.com/cli/v6/commands/npm-link
[5]: https://github.com/angular/angular/issues/25813#issuecomment-440288681


# Snippets

`/* Update the query via a subscription (i.e. server push) instead of polling */
    /* this.existingConversationsQueryRef.subscribeToMore({
      document: NewConversationRequestedDocument,
      variables: {
        recipient: this.appId,
      },
      updateQuery: (prev, { subscriptionData }) => {
        subscriptionData = subscriptionData as ApolloQueryResult<NewConversationRequestedSubscription>;

        if (!subscriptionData.data) {
          return prev;
        }

        const newConversations =
          (subscriptionData as ApolloQueryResult<NewConversationRequestedSubscription>).data?.queryConversation?.filter(
            (conversation) =>
              !prev.queryConversation?.some(
                (oldConversation) => oldConversation?.id === conversation?.id
              )
          ) || [];

        console.log(newConversations);

        return {
          queryConversation: [...newConversations, ...prev.queryConversation!],
        };
      },
    }); */`