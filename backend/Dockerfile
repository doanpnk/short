FROM golang:1.13.1-alpine AS builder

WORKDIR /short

RUN apk add --no-cache git bash

# Install dependencies
COPY go.mod go.sum ./
RUN go mod download

# Verify dependencies
RUN go mod verify

COPY . .

RUN go build -o build/app main.go

FROM alpine:3.10 as production

WORKDIR /short

RUN apk add --no-cache bash

COPY --from=builder /short/build/app ./build/app
COPY --from=builder /short/scripts/wait-for-it ./scripts/wait-for-it
COPY --from=builder /short/app/adapter/sqldb/migration ./app/adapter/sqldb/migration
COPY --from=builder /short/app/adapter/routing/public ./app/adapter/routing/public
COPY --from=builder /short/app/adapter/routing/api.yml ./app/adapter/routing/api.yml
COPY --from=builder /short/app/adapter/gqlapi/schema.graphql ./app/adapter/gqlapi/schema.graphql

ENV ENV=production
ENV DB_HOST=localhost
ENV DB_PORT=5432
ENV DB_USER=short
ENV DB_PASSWORD=short
ENV DB_NAME=short
ENV RECAPTCHA_SECRET=your_recaptcha_secret
ENV GITHUB_CLIENT_ID=your_github_id
ENV GITHUB_CLIENT_SECRET=your_client_secret
ENV FACEBOOK_CLIENT_ID=facebook_client_id
ENV FACEBOOK_CLIENT_SECRET=facebook_client_secret
ENV FACEBOOK_REDIRECT_URI=http://localhost/oauth/facebook/sign-in/callback
ENV GOOGLE_CLIENT_ID=google_client_id
ENV GOOGLE_CLIENT_SECRET=google_client_secret
ENV GOOGLE_REDIRECT_URI=http://localhost/oauth/google/sign-in/callback
ENV JWT_SECRET=Kb6wBPMcygy98RdvdzkBeB99sMja6PeU
ENV WEB_FRONTEND_URL=https://shorturl.primedata.ai
ENV KEY_GEN_BUFFER_SIZE=10
ENV KEY_GEN_HOSTNAME=kgs-staging.primedata.ai
ENV KEY_GEN_PORT=8080
ENV GRAPHQL_API_PORT=8088
ENV GRAPHQL_SCHEMA_PATH=app/adapter/gqlapi/schema.graphql
ENV GRAPH_I_QL_DEFAULT_QUERY="query {}"
ENV HTTP_API_PORT=8090
ENV GRPC_API_PORT=8081
ENV AUTH_TOKEN_LIFETIME=520w
ENV SEARCH_API_TIMEOUT=1s
ENV DATA_DOG_API_KEY=data_dog_api_key
ENV SEGMENT_API_KEY=segment_api_key
ENV IP_STACK_API_KEY=ip_stack_api_key
ENV GOOGLE_API_KEY=your_google_api_key

CMD ["./build/app", "start"]