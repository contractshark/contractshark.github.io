# contract-shark

contract-shark automates the testing process of your Solidity code. It doesn't require you to install certain applications in order to get started.

> Note Contract Shark is not publicly available yet

### Overview

The core test functionality is provided by the `@contract-shark/qq` module which is automatically attached to your project at initialization. Here we explain some of the main framework features but please explore the source code to find out all the possibilities.

#### Getting Started 

The framework provides a `Spec` class which holds basically the whole testing power. You start your test by creating an instance of that class.

```ts
import { Threat } from "@contract-shark/qq";

const threat = new Threat();
```

#### Testing features

The Spec instance provides methods that you can use when writing tests. Most of the time you will use the `test` method which performs the test you write.

```ts
threat.test("is flashloan", async (ctx) => {
  // promise | function
  ctx.true(true);
});
```

There is also the `skip` method which prevents a test to be performed, and the `only` method which includes itself into the test process but excludes all other tests.


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

These methods have access to the `stage` of the spec instance. The stage is global to the whole stack which means that all settings are always preserved.

There are also the `beforeEach` and `afterEach` methods which are triggered before and after each test. These methods have access to the `context` and `stage` of the spec. The context represents a copy of a stage and is preserved between `beforeEach`, `test` and `afterEach` methods. This allows for testing atomic tests where a fresh context is always created for each test.

```ts
cshark.beforeEach((context, stage) => {
  // execute before all tests
});
...
cshark.afterEach((context, stage) => {
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

const threat = new Threat<Playbook>();
```

