# contract-shark

Contract Shark is a lightweight, open source, magic-free framework for testing smart contracts written in [Solidity](https://solidity.readthedocs.io/). The testing suite is built on top of the [Hayspec framework](https://github.com/hayspec/framework) thus using [TypeScript](https://www.typescriptlang.org/) is supported.

Contract Shark provides development environment for the Ethereum blockchain and includes useful tools which enable developers to easily write tests for smart contracts.

The source code is available on [GitHub](https://github.com/contract-shark/framework) where you can also find our [issue tracker](https://github.com/contract-shark/framework/issues).

## Installation

Start by installing the contract-shark command-line tool.

```bash
$ npm install -g @contract-shark/cli web3 solc
```

contract-shark depends on the latest [web3](https://www.npmjs.com/package/web3) and [solc](https://www.npmjs.com/package/solc) packages, so make sure you have them globally installed. contract-shark also uses promises thus you need to use [Promise polyfill](https://github.com/taylorhakes/promise-polyfill) when promises are not supported.

## Getting started

contract-shark automates the testing process of your Solidity code. It doesn't require you to install certain applications in order to get started.

The contract-shark interface is designed to fully support the power of [TypeScript](https://www.typescriptlang.org/) when writing tests. It is magic-free which means you have a complete control and visibility of what the code does and how tests are executed. The testing flow should look familiar to any JavaScript or TypeScript developer.

### Project initialization

Start by creating a new project folder.

```bash
$ mkdir myproject
$ cd myproject
```

Initialize the project and install the dependencies.

```bash
$ contract-shark init
$ npm install
```

Run tests to verify everything works as expected.

```bash
$ npm test
```

### Writing tests

The core test functionality is provided by the `@contract-shark/spec` module which is automatically attached to your project at initialization. Here we explain some of the main framework features but please explore the source code to find out all the possibilities.

#### Initializing specs

The framework provides a `Spec` class which holds basically the whole testing power. You start your test by creating an instance of that class.

```ts
import { Spec } from "@contract-shark/spec";

const spec = new Spec();
```

#### Testing features

The Spec instance provides methods that you can use when writing tests. Most of the time you will use the `test` method which performs the test you write.

```ts
spec.test("is true", async (ctx) => {
  // promise | function
  ctx.true(true);
});
```

There is also the `skip` method which prevents a test to be performed, and the `only` method which includes itself into the test process but excludes all other tests.

#### Nested specs

Tests can be nested using the `spec` method.

```ts
const colors = new Spec();
...
spec.spec('colors', colors);
```

#### Using callbacks

The framework provides `before` and `after` methods which are execute at the beginning and at the end of the spec case.

```ts
spec.before((stage) => {
  // execute before all tests
});
...
spec.after((stage) => {
  // execute after all tests
});
```

These methods have access to the `stage` of the spec instance. The stage is global to the whole spec stack which means that all settings are always preserved.

There are also the `beforeEach` and `afterEach` methods which are triggered before and after each test. These methods have access to the `context` and `stage` of the spec. The context represents a copy of a stage and is preserved between `beforeEach`, `test` and `afterEach` methods. This allows for testing atomic tests where a fresh context is always created for each test.

```ts
spec.beforeEach((context, stage) => {
  // execute before all tests
});
...
spec.afterEach((context, stage) => {
  // execute after all tests
});
```

Callback functions can be called multiple times and the execution will happen in a defined sequence.

#### Shared data

The `context` and the `stage` both provide a way to `set` and `get` values with proper TypeScript types.

```ts
interface Data {
  id: number;
  name: string;
}

const spec = new Spec<Data>();

spec.beforeEach((ctx) => {
  ctx.set("id", 100);
  ctx.set("name", "John");
});

spec.test("is John with id=100", (ctx) => {
  const id = ctx.get("id");
  const name = ctx.get("name");
  ctx.is(id, 100);
  ctx.is(name, "John");
});
```

Values set inside the `before` and `after` blocks are available to all `spec` methods. Values set in the `beforeEach` and `afterEach` blocks are available only on the context stack of each test.

#### Contract deployment

Stage and context both provide a series of different helper methods.

A very important method is the `deploy()` method which deploys a contract to a local blockchain process in the background and returns a contract instance.

```ts
const { instance, receipt } = await ctx.deploy({
  src: "./contracts.json",
  contract: "Main",
});
```

### Using CLI

The `@contract-shark/cli` module is automatically installed when you initialize the project. You can interact with the utility using the `npx contract-shark` command in your terminal.

To get a list of available features use the `--help` flag.

```
$ npx contract-shark --help
```

#### Running tests

Every test file must export the `spec` instance for the CLI to be able to detect the test.

```ts
export default spec;
```

Run the `contract-shark test` command to run tests. Customize the files search by using the `--match` flag.

```
$ npx contract-shark test --match ./**/*.test.*
```

#### TypeScript support

Install the [ts-node](https://www.npmjs.com/package/ts-node) NPM package then use the `--require` flag to enable [TypeScript](https://www.typescriptlang.org/) support.

```
contract-shark --require ts-node/register
```

#### Project configuration

contract-shark configuration options can be saved inside the package.json file under the the `contract-shark` key.

```json
{
  "contract-shark": {
    "compiler": {
      "build": "./build",
      "match": ["./src/**/*.sol"],
      "severities": ["error", "warning"],
      "evmVersion": "byzantium"
    },
    "flattener": {
      "build": "./build",
      "match": ["./src/**/*.sol"],
      "severities": ["error", "warning"]
    },
    "sandbox": {
      "port": 8545,
      "host": "localhost"
    },
    "test": {
      "server": true,
      "port": 8545,
      "host": "localhost",
      "match": ["./src/**/*.test.*"]
    },
    "require": ["ts-node/register"]
  }
}
```

Note that these options can be overriden by providing CLI arguments.

### Using continuous integration

For a full example of a Solidity contract repository including continuous integration using Travis and contract-shark, see [URL]

## Packages

| Package                                                                                                                | Description                    | Version |
| ---------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ------- |
| [@contract-shark/cli](https://github.com/contract-shark/framework/tree/master/packages/contract-shark-cli)             | Command-line interface.        |
| [@contract-shark/compiler](https://github.com/contract-shark/framework/tree/master/packages/contract-shark-compiler)   | Smart contracts compiler.      |
| [@contract-shark/flattener](https://github.com/contract-shark/framework/tree/master/packages/contract-shark-flattener) | Smart contracts flattener.     |
| [@contract-shark/init](https://github.com/contract-shark/framework/tree/master/packages/contract-shark-init)           | Project structure initializer. |
| [@contract-shark/sandbox](https://github.com/contract-shark/framework/tree/master/packages/contract-shark-sandbox)     | Ethereum sandbox server.       |
| [@contract-shark/spec](https://github.com/contract-shark/framework/tree/master/packages/contract-shark-spec)           | Core test suite.               |

@contract-shark/spec

## Contributing

See [CONTRIBUTING.md](https://github.com/contract-shark/framework/blob/master/CONTRIBUTING.md) for how to help out.

## Licence

See [LICENSE](https://github.com/contract-shark/framework/blob/master/LICENCE) for details.
