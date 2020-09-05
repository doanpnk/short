# Short

[![Build Status](https://ci.time4hacks.com/api/badges/short-d/short/status.svg)](https://ci.time4hacks.com/short-d/short)
[![codecov](https://codecov.io/gh/short-d/short/branch/master/graph/badge.svg)](https://codecov.io/gh/short-d/short)
[![Maintainability](https://api.codeclimate.com/v1/badges/910f974653f1b3495534/maintainability)](https://codeclimate.com/github/short-d/short/maintainability)
[![Go Report Card](https://goreportcard.com/badge/github.com/short-d/short)](https://goreportcard.com/report/github.com/short-d/short)
[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/byliuyang/short)
[![Floobits Status](https://floobits.com/byliuyang/short.svg)](https://floobits.com/byliuyang/short/redirect)

![Demo](promo/marquee.png)

## Preview

![Demo](doc/demo.gif)

## Get `s/` Chrome extension

Install it from [Chrome Web Store](https://short-d.com/r/ext) or build it
from [source](https://short-d.com/r/ext-code)

## Dependent Projects

- [app](https://github.com/short-d/app): Reusable framework for Go apps & command
   line tools.
- [kgs](https://github.com/short-d/kgs): Offline unique key generation service.

## Table of Contents

1. [Getting Started](#getting-started)
   1. [Accessing the source code](#accessing-the-source-code)
   1. [Prerequisites](#prerequisites)
   1. [Local environmental variables](#local-environmental-variables)
   1. [Create reCAPTCHA account](#create-recaptcha-account)
   1. [Configure Single Sign On](#configure-single-sign-on)
   1. [Backend](#backend)
   1. [Frontend](#frontend)
1. [System Design](#system-design)
   1. [App Level Architecture](#app-level-architecture)
   1. [Service Level Architecture](#service-level-architecture)
   1. [Object Oriented Design](#object-oriented-design)
   1. [Dependency Injection](#dependency-injection)
   1. [Database Modeling](#database-modeling)
   1. [Feature Toggle](#feature-toggle)
   1. [Search Engine Optimization](#search-engine-optimization)
   1. [Social Media Summary Card](#social-media-summary-card)
1. [Testing](#testing)
   1. [The Importance Of Automation](#the-importance-of-automation)
   1. [Testing Strategy](#testing-strategy)
   1. [Unit Testing](#unit-testing)
   1. [Integration Testing](#integration-testing)
   1. [Component Testing](#component-testing)
   1. [Contract Testing](#contract-testing)
   1. [End To End Testing](#end-to-end-testing)
   1. [The Test Pyramid](#the-test-pyramid)
1. [Deployment](#deployment)
   1. [Continuous Delivery](#continuous-delivery)
   1. [Kubernetes](#kubernetes)
   1. [GitOps](#gitops)
1. [Tools We Use](#tools-we-use)
1. [Contributing](#contributing)
1. [Author](#author)
1. [License](#license)

## Getting Started

### Accessing the source code

```bash
git clone https://github.com/short-d/short.git
```

### Prerequisites

- [Go](https://golang.org/doc/install) v1.13.1
- [Node.js](https://nodejs.org/en/download/) v12.12.0
- [Yarn](https://classic.yarnpkg.com/en/docs/install) v1.19.1
- [PostgreSQL](doc/tutorial/POSTGRES.md) v12.0

### Local environmental variables

1. Copy `backend/.env.dist` file to `backend/.env`:

   ```bash
   cp backend/.env.dist backend/.env
   ```

1. Copy `frontend/.env.development.dist` file to `frontend/.env.development`:

   ```bash
   cp frontend/.env.development.dist frontend/.env.development
   ```


### Create reCAPTCHA account

1. Sign up at [ReCAPTCHA](https://short-d.com/r/recaptcha) with the
   following configurations:

   | Field           | Value          |
   |-----------------|----------------|
   | Label           | `Short`        |
   | reCAPTCHA type  | `reCAPTCHAv3`  |
   | Domains         | `localhost`    |

1. Open `settings`. Copy `SITE KEY` and `SECRET KEY`.

1. Replace the value of `RECAPTCHA_SECRET` in the `backend/.env` file with
   `SECRET KEY`.
1. Replace the value of `REACT_APP_RECAPTCHA_SITE_KEY` in
   `frontend/.env.development` file with `SITE KEY`.

### Configure Single Sign On
#### Google

Create a new Client ID at
   [Google API Credentials](https://console.developers.google.com/apis/credentials):

1. Click on `Create Credentials` and select `OAuth client ID`.

1. Select `Web application` for `Application type`.

1. Fill in `http://localhost/oauth/google/sign-in/callback` for `Authorized redirect URIs` and click on `Create`.
 
1. Replace the value of `GOOGLE_CLIENT_ID` in `backend/.env` file with `Your Client ID`.
1. Replace the value of `GOOGLE_CLIENT_SECRET` in `backend/.env` file with
   `Your Client Secret`.

#### Facebook
You can find the detailed instructions on setting up Facebook sign in [here](doc/sso/FACEBOOK.md) in case you are interested in. 

#### Github
You can find the detailed instructions on setting up Github sign in [here](doc/sso/GITHUB.md) in case you are interested in.
   
### Backend

1. Update placeholder values with your own configurations.

1. Launch backend server

   ```bash
   cd backend
   ./scripts/dev
   ```

1. Remember to install developers tools before start coding:

   ```bash
   ./scripts/tools
   ```

### Frontend

1. Update `REACT_APP_RECAPTCHA_SITE_KEY` in `frontend/.env.development`.

1. Launch frontend server

   ```bash
   cd frontend
   ./scripts/dev
   ```

1. Visit [http://localhost:3000](http://localhost:3000)

## System Design

### App Level Architecture

Short backend is built on top of
[Uncle Bob's Clean Architecture](https://api.short-d.com/r/ca), the central
objective of which is separation of concerns.

![Short Backend](doc/clean-architecture/short-backend.jpg)

It enables the developers to modify a single component of the system at a time
while leaving the rest unchanged. This minimizes the amount of changes have to
be made in order to support new requirements as the system grows. Clean
Architecture also improves the testability of system, which in turn saves
precious time when creating automated tests.

### Service Level Architecture

Short adopts [Microservices Architecture](https://api.short-d.com/r/ms) to
organize dependent services around business capabilities and to enable
independent deployment of each service.

![Short Cloud](doc/cloud/overall.jpg)
[SSR](https://docs.google.com/document/d/16iV91aESfnYU6rIEWGEzws3nbDX3hB-St9gAxrtCAa8), 
[Toggle](https://docs.google.com/document/d/1TuWexeKwhQh8JTytRAwST3XujBi0wTGExwJan-WfXWs),
[Status Page](https://docs.google.com/document/d/1pgRNnD8yAlEmj-sucS_FZ89LdvBy5zpKQ9OvILoBqDM), Search,
[Data Reporter](https://docs.google.com/document/d/1-BtxBuS4zIk8H1oXDe-qqEccWp4v6aT2GrWBfwIX5oI),
[Feedback Widget](https://docs.google.com/document/d/1IoaTMHsOi5Tb0ZV4btxsvUnKplKi2lxaIYU600cwRuc),
 and Cloud API are still under active development.

### Object Oriented Design

Short leverages class design, package cohesion, and package coupling principles
to manage logical dependency between internal components.

#### Class Design

| Principal                                                        | Description                                                            |
|------------------------------------------------------------------|------------------------------------------------------------------------|
| [Single Responsibility Principle](https://api.short-d.com/r/srp) | A class should have one, and only one, reason to change.               |
| [Open Closed Principle](https://api.short-d.com/r/ocp)           | You should be able to extend a classes behavior, without modifying it. |
| [Liskov Substitution Principle](https://api.short-d.com/r/lsp)   | Derived classes must be substitutable for their base classes.          |
| [Interface Segregation Principle](https://api.short-d.com/r/isp) | Make fine grained interfaces that are client specific.                 |
| [Dependency Inversion Principle](https://api.short-d.com/r/dip)  | Depend on abstractions, not on concretions.                            |

#### Package Cohesion

| Principal                                                            | Description                                           |
|----------------------------------------------------------------------|-------------------------------------------------------|
| [Release Reuse Equivalency Principle](https://api.short-d.com/r/rep) | The granule of reuse is the granule of release.       |
| [The Common Closure Principle](https://api.short-d.com/r/ccp)        | Classes that change together are packaged together.   |
| [The Common Reuse Principle](https://api.short-d.com/r/crp)          | Classes that are used together are packaged together. |

#### Package Coupling

| Principal                                                       | Description                                           |
|-----------------------------------------------------------------|-------------------------------------------------------|
| [Acyclic Dependencies Principle](https://api.short-d.com/r/adp) | The dependency graph of packages must have no cycles. |
| [Stable Dependencies Principle](https://api.short-d.com/r/sdp)  | Depend in the direction of stability.                 |
| [Stable Abstractions Principle](https://api.short-d.com/r/sap)  | Abstractness increases with stability.                |

### Dependency Injection

Short produces flexible and loosely coupled code, by explicitly providing
components with all of the dependencies they need.

```go
type Authenticator struct {
  tokenizer          fw.CryptoTokenizer
  timer              fw.Timer
  tokenValidDuration time.Duration
}

func NewAuthenticator(
  tokenizer fw.CryptoTokenizer,
  timer fw.Timer,
  tokenValidDuration time.Duration,
) Authenticator {
  return Authenticator{
    tokenizer:          tokenizer,
    timer:              timer,
    tokenValidDuration: tokenValidDuration,
  }
}
```

Short also simplifies the management of the big block of order-dependent
initialization code with [Wire](https://api.short-d.com/r/wire), a compile time
dependency injection framework by Google.

```go
func InjectGraphQlService(
  name string,
  sqlDB *sql.DB,
  graphqlPath provider.GraphQlPath,
  secret provider.ReCaptchaSecret,
  jwtSecret provider.JwtSecret,
  bufferSize provider.KeyGenBufferSize,
  kgsRPCConfig provider.KgsRPCConfig,
  tokenValidDuration provider.TokenValidDuration,
) (mdservice.Service, error) {
  wire.Build(
    wire.Bind(new(fw.GraphQlAPI), new(graphql.Short)),
    wire.Bind(new(url.Retriever), new(url.RetrieverPersist)),
    wire.Bind(new(url.Creator), new(url.CreatorPersist)),
    wire.Bind(new(repo.UserURLRelation), new(db.UserURLRelationSQL)),
    wire.Bind(new(repo.URL), new(*db.URLSql)),
    wire.Bind(new(keygen.KeyGenerator), new(keygen.Remote)),
    wire.Bind(new(service.KeyFetcher), new(kgs.RPC)),

    observabilitySet,
    authSet,

    mdservice.New,
    provider.NewGraphGophers,
    mdhttp.NewClient,
    mdrequest.NewHTTP,
    mdtimer.NewTimer,

    db.NewURLSql,
    db.NewUserURLRelationSQL,
    provider.NewRemote,
    url.NewRetrieverPersist,
    url.NewCreatorPersist,
    provider.NewKgsRPC,
    provider.NewReCaptchaService,
    requester.NewVerifier,
    graphql.NewShort,
  )
  return mdservice.Service{}, nil
}
```

### Database Modeling

![Entity Relation Diagram](doc/db/er.jpg)

### Feature Toggle

Short employs `feature toggles` to modify system behavior without changing code.
UI components controlled by the feature toggles are created inside a centralized
`UIFactory` in order to avoid having nested `if` `else` statement across the
code base:

```typescript
// UIFactory.tsx
export class UIFactory {
  constructor(
    private featureDecisionService: IFeatureDecisionService
  ) {}

  public createGoogleSignInButton(): ReactElement {
    if (!this.featureDecisionService.includeGoogleSignButton()) {
      return <div />;
    }
    return (
      <GoogleSignInButton
        googleSignInLink={this.authService.googleSignInLink()}
      />
    );
  }

  public createGithubSignInButton(): ReactElement {
    if (!this.featureDecisionService.includeGithubSignButton()) {
      return <div />;
    }
    return (
      <GithubSignInButton
        githubSignInLink={this.authService.githubSignInLink()}
      />
    );
  }
}
```

Short also provides `IFeatureDecisionService` interface, allowing the developers
to switch to dynamic feature toggle backend in the future by simply swapping
the dependency injected.

```typescript
// FeatureDecision.service.ts
export interface IFeatureDecisionService {
  includeGithubSignButton(): boolean;
  includeGoogleSignButton(): boolean;
  includeFacebookSignButton(): boolean;
}
```

```typescript
// StaticConfigDecision.service.ts
import { IFeatureDecisionService } from './FeatureDecision.service';

export class StaticConfigDecisionService implements IFeatureDecisionService {
  includeGithubSignButton(): boolean {
    return false;
  }
  includeGoogleSignButton(): boolean {
    return false;
  }
  includeFacebookSignButton(): boolean {
    return true;
  }
}
```

```typescript
// dep.ts
export function initUIFactory(
  ...
): UIFactory {
  ...
  const staticConfigDecision = new StaticConfigDecisionService();
  ...
  return new UIFactory(
    ...,
    staticConfigDecision
  );
}
```

You can read about the detailed feature toggle design on
[this article](https://martinfowler.com/articles/feature-toggles.html).

### Search Engine Optimization

In order to improve the quality and quantity of the website's traffic, Short
increases its visibility to web search engines through HTML meta tags.

```html
<!-- ./frontend/public/index.html -->
<title>Short: Free online link shortening service</title>

<!-- Search Engine Optimization -->
<meta name="description"
      content="Short enables people to type less for their favorite web sites">
<meta name="robots" content="index, follow">
<link href="https://short-d.com" rel="canonical">
```

If you search `short-d.com` on Google, you should see Short shows up as
the first result:

![Google Search Result](doc/seo/google.jpg)

### Social Media Summary Card

#### Facebook & LinkedIn

Short leverages `Open Graph` tags to control what content shows up in
the summary card when the website is shared on Facebook or LinkedIn:

```html
<!-- ./frontend/public/index.html -->
<!-- Open Graph -->
<meta property="og:title" content="Short: Free link shortening service"/>
<meta property="og:description"
      content="Short enables people to type less for their favorite web sites"/>
<meta property="og:image"
      content="https://short-d.com/promo/small-tile.png"/>
<meta property="og:url" content="https://short-d.com"/>
<meta property="og:type" content="website"/>
```

Shared on Facebook:

![Facebook Card](doc/social-media-card/facebook.jpg)

Shared on LinkedIn:

![LinkedIn Card](doc/social-media-card/linkedin.jpg)

#### Twitter

Twitter uses its own meta tags to determine what will show up when
the website is mentioned in a Tweet:

```html
<!-- Twitter -->
<meta name="twitter:card" content="summary_large_image"/>
<meta name="twitter:site" content="@byliuyang11"/>
<meta name="twitter:title" content="Short: Free link shortening service"/>
<meta name="twitter:description"
      content="Short enables people to type less for their favorite web sites"/>
<meta name="twitter:image" content="https://short-d.com/promo/twitter-card.png"/>
```

![Twitter Card](doc/social-media-card/twitter.jpg)

## Testing

### The Importance Of Automation

Short is maintained by a small team of talented software engineers working
at Google, Uber, and Vmware as a side project. The team wants to deliver new
features faster without sacrificing its quality. Testing ever-increasing
amount of features manually soon becomes impossible — unless we want
to spend all our time with manual, repetitive work instead of delivering
working features.

Test automation is the only way forward.

### Testing Strategy

![Test Strategy](doc/testing/test-strategy.png)

Please read [Testing Strategies in a Microservice Architecture](https://martinfowler.com/articles/microservice-testing)
for a detailed introduction on test strategies.

### Unit Testing

A unit test exercises the smallest piece of testable software in the
application to determine whether it behaves as expected.

![Unit Test](doc/testing/unit-test.png)

Run unit tests for backend:

```bash
cd backend
./scripts/unit-test
```

#### Sociable And Solitary

![Two Types of Unit Test](doc/testing/unit-test-two-types.png)

#### The FIRST Principal

- [F]ast: Unit tests should be fast otherwise they will slow down
   development & deployment.
- [I]ndependent: Never ever write tests which depend on other test cases.
- [R]epeatable: A repeatable test is one that produces the same results
   each time you run it.
- [S]elf-validating: There must be no manual interpretation of the results.
- [T]imely/[T]horoughly: Unit tests must be included for every pull request
   of a new feature and cover edge cases, errors, and bad inputs.

#### Test Structure

An automated test method should be composed of 3As: Arrange, Act, and Assert.

- [A]rrange: All the data needed for a test should be arranged as part
  of the test. The data used in a test should not depend on the environment
  in which the test is running.
- [A]ct: Invoke the actual method under test.
- [A]ssert: A test method should test for a single logical outcome.

### Integration Testing

An integration test verifies the communication paths and interactions
between components to detect interface defects.

![Integration Test](doc/testing/integration-test.png)

Run integration tests for backend:

```bash
cd backend
./scripts/integration-test
```

### Component Testing

A component test limits the scope of the exercised software to a portion
of the system under test, manipulating the system through internal code
interfaces and using test doubles to isolate the code under test from
other components.

#### In Process

![Component Test](doc/testing/component-test-in-process.png)

#### Out Of Process

![Component Test](doc/testing/component-test-out-of-process.png)

### Contract Testing

An integration contract test is a test at the boundary of an external
service verifying that it meets the contract expected by a consuming
service.

### End To End Testing

An end-to-end test verifies that a system meets external requirements
and achieves its goals, testing the entire system, from end to end.

### The Test Pyramid

![Test Pyramid](doc/testing/test-pyramid.png)

## Deployment

### Continuous Delivery
![Continuous Delivery](doc/deployment/cd-pipeline.jpg)

Currently, we use continuous delivery to deploy code changes to staging & 
production environment. 

Merging pull request into master branch on Github will automatically deploy the
changes to [staging](https://staging.short-d.com). Merging from `master` branch
to `production` branch will automatically deploy the latest code to the production.

In the future, when after we add enough automated tests, we may migrate to 
continuous deployment instead for faster releases.

You can find the differences between continuous delivery & continuous deployment [here](doc/tutorial/CI-CD.md)

### Kubernetes

Short leverages [Kubernetes](https://kubernetes.io) to automate deployment, scaling,
and management of containerized microservices.

![Node overview](https://d33wubrfki0l68.cloudfront.net/5cb72d407cbe2755e581b6de757e0d81760d5b86/a9df9/docs/tutorials/kubernetes-basics/public/images/module_03_nodes.svg)

### GitOps
Short uses [GitOps](https://github.com/byliuyang/gitops) to configure Kubernetes
cluster and span up new services.

![Git Ops](doc/deployment/gitops.jpg)

## Tools We Use

- [Drone](https://short-d.com/r/ci): Continuous integration
  written in Go
- [Code Climate](https://codeclimate.com/): Automated code
  review
- [ElephantSQL](https://www.elephantsql.com): Managed PostgreSQL service.

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code
of conduct, the process for submitting pull requests to us, and our code
review guideline.

## Author

Harry Liu - *Initial work* - [byliuyang](https://short-d.com/r/ghharry)

As the tech lead of Short, I am responsible for the overall planning, execution
and success of complex software solutions to meet users' needs.

I deeply believe in and am striving to achieve the right column of the
following diagram:

![Manager vs Leader](doc/leader-vs-manager.jpg)

## License

This project is maintained under MIT license
