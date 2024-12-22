# GoCardless CLI

The GC CLI is a developer tool that helps developers to build, test and manage their integration with GC, directly from their terminal.

## Usage

### Login 

Before running most commands, you need to login to the CLI as a Merchant.

```shell
gc login
```

This will open up the GoCardless Connect Login page and then you can login as the 
merchant you want to interact with. If you don't have a merchant, you can also signup
using the connect page.

### HTTP Apis & Resources

#### Get resources(payments, events, mandates, etc)
```
gc get payment PY12345
```

#### List resources(payments, events, mandates, etc)
```
gc list payments
```

#### Create resource (payments, events, mandates, etc)

input params based on the api, ex:  -d currency='USD' \ -d amount='1000' for create payment
```
gc create payment -d currency='USD' \ -d amount='1000' -d 'links[mandate]=MD123'
```

#### Delete resource (payments, events, mandates, etc)
``` 
c

#### Listen for webhooks
Listens for any new webhooks, and either forwards them to the provided
URL, or prints them on the console if no URL is provided.
``` 
gc listen --forward "http://localhost:8001/webhook"
```
### Browser Shortcuts

#### Open 
opens gocardless pages on browser
```
c

## Contributing

### Dependencies

The following dependencies are required to build or run the CLI
- Go (>= 1.17)

### Getting started

To start up the cli, run the [main.go](./main.go) file with the command:

```
go run main.go
```

The CLI App requires that a GoCardless Apps credentials be provided for it to make requests on behalf
of authorized merchants. To configure the CLI App's credentials, create a copy of the `.env.sample` file and rename
it as a `.env` file. Update the CLIENT_* credentials in that file to those of your Sandbox Partner App. See instruction [here](https://developer.gocardless.com/getting-started/partners/connecting-your-users-accounts/#creating-an-app)
on how to create a GoCardless App. Also ensure your Apps OAuth redirect url is `http://localhost:8080/callback`.

Once the credentials are set, running `go run main.go` will read the credentials from the `.env` file. You can
then proceed to authorize a merchant to the app using `go run main.go login`

You can execute some of the commands by passing them after the filename. For example if you want to run
the `version` command, run:

```shell
go run main.go version
```

### Testing and Linting

Run the following command to run tests:

```shell
make test
```

> This codebase uses ginkgo as its testing framework. You should read up on how to it runs specs 
> [here](https://onsi.github.io/ginkgo/#running-specs) to better understand how it works.


You can also perform lint and code formatting checks with the command:

```shell
make lint
```

#### Generating Test Mocks

This codebase uses [mockery](https://github.com/vektra/mockery) to generate mocks and the preferred way to invoke
the mockery command is through `go generate`.

First, ensure you have mockery (v2) installed. You'll only need to run this once:

```shell
go install github.com/vektra/mockery/v2@latest
```

Next, run `make gen-go`, this will run all existing mockery `//go:generate` comments in go files.
This will generate the respective `mocks/*` files for your interfaces or functions.


### Building a Standalone Binary

To build a standalone binary, run the make command:

```shell
make build
```

This will output a `gc` executable in the `dist` directory. You can run commands on the output like this:

```shell
dist/gc version
```

### Deployment

When ready to release a new version of the cli.

1. Create a new branch for the release. The branch should typically be named something like `release/vX.X.X` (and commit message with `release:` prefix), so it is easy to track.
2. In the release branch, update the version number in the `CLI_VERSION` file to the new release version. Release versions should follow [semantic versioning](https://semver.org/) conventions.
3. Open a PR for the release branch to `master`. This PR should contain detailed description of the features, fixes, updates in this release.
4. Merge in the PR when ready. The CD pipeline will recognize the change in the CLI_VERSION file and create a new release. See `.goreleaser.yaml` for channels we release to.


> NOTE: If the new release contains changes to command documentations, then you'll need to release the update in the developer docs. To do that:
> 
> After your PR get's merged and a release is created (check the #connect-alerts channel to know when). Head over to the developer docs and follow the [instructions in the README](https://github.com/gocardless/developer.gocardless.com#deployment) to generate a CLI reference for the new versions).



### Configuration and Environment Variables

Look into the [.env.sample](./.env.sample) file to see example of environment variable needed to run the CLI in dev mode.

We typically substitute environment variables into the final release binary during linking using `ldflag`'s.
See the [.goreleaser.yaml](.goreleaser.yaml) file for examples of how we do this.

### Structure

The general structure of the codebase is as in the image below:

![Architecture Image](./docs/code-structure-overview.png)

- Cobra Commands are created within the `cmd` package for the respective commands. These commands parse the flags and arguments and execute respective action objects.
- Actions are responsible for the core business logic to respond to a command. They are located in the `actions` package.
- InteractiveIO should be used in actions to interact output information.
- Actions depend on other specific interfaces like `session.Handler`, `api.Client` etc. Feel free to explore the other packages.

### Infrastructure


| System | Purpose                                                      |
|--------|--------------------------------------------------------------|
| CircleCI | CI/CD Pipeline for building, testing and publishing releases |

### Monitoring and Alerting

| System | Purpose |
| --- | --- |
| [Sentry](https://sentry.io/gocardless/gc-cli/) | Alerting on exceptions |


## Learning resources

- [Go Tutorial](https://go.dev/doc/tutorial/getting-started)
- [Cobra Docs](https://github.com/spf13/cobra) and [Tutorial](https://towardsdatascience.com/how-to-create-a-cli-in-golang-with-cobra-d729641c7177)

## License

Apache 2.0

