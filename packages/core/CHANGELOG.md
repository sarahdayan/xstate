# xstate

## 5.0.0-next.0

### Major Changes

- [`e09efc7`](https://github.com/davidkpiano/xstate/commit/e09efc720f05246b692d0fdf17cf5d8ac0344ee6) [#878](https://github.com/davidkpiano/xstate/pull/878) Thanks [@Andarist](https://github.com/Andarist)! - Removed third parameter (context) from Machine's transition method. If you want to transition with a particular context value you should create appropriate `State` using `State.from`. So instead of this - `machine.transition('green', 'TIMER', { elapsed: 100 })`, you should do this - `machine.transition(State.from('green', { elapsed: 100 }), 'TIMER')`.

* [`3de36bb`](https://github.com/davidkpiano/xstate/commit/3de36bb24e8f59f54d571bf587407b1b6a9856e0) [#953](https://github.com/davidkpiano/xstate/pull/953) Thanks [@davidkpiano](https://github.com/davidkpiano)! - Support for getters as a transition target (instead of referencing state nodes by ID or relative key) has been removed.

  The `Machine()` and `createMachine()` factory functions no longer support passing in `context` as a third argument.

  The `context` property in the machine configuration no longer accepts a function for determining context (which was introduced in 4.7). This might change as the API becomes finalized.

  The `activities` property was removed from `State` objects, as activities are now part of `invoke` declarations.

  The state nodes will not show the machine's `version` on them - the `version` property is only available on the root machine node.

  The `machine.withContext({...})` method now permits providing partial context, instead of the entire machine context.

- [`97b0569`](https://github.com/davidkpiano/xstate/commit/97b05690cd8b30824eb176c813a145d3ef0d2a78) [#901](https://github.com/davidkpiano/xstate/pull/901) Thanks [@davidkpiano](https://github.com/davidkpiano)! - Simplified invoke definition: the `invoke` property of a state definition will now only accept an `InvokeCreator`, which is a function that takes in context, event, and meta (parent, id, etc.) and returns an `Actor`.

  ```diff
  - invoke: someMachine
  + invoke: spawnMachine(someMachine)

  - invoke: (ctx, e) => somePromise
  + invoke: spawnPromise((ctx, e) => somePromise)

  - invoke: (ctx, e) => (cb, receive) => { ... }
  + invoke: spawnCallback((ctx, e) => (cb, receive) => { ... })

  - invoke: (ctx, e) => someObservable$
  + invoke: spawnObservable((ctx, e) => someObservable$)
  ```

  This also includes a helper function for spawning activities:

  ```diff
  - activity: (ctx, e) => { ... }
  + invoke: spawnActivity((ctx, e) => { ... })
  ```

* [`e09efc7`](https://github.com/davidkpiano/xstate/commit/e09efc720f05246b692d0fdf17cf5d8ac0344ee6) [#878](https://github.com/davidkpiano/xstate/pull/878) Thanks [@Andarist](https://github.com/Andarist)! - Support for compound string state values has been dropped from Machine's transition method. It's no longer allowed to call transition like this - `machine.transition('a.b', 'NEXT')`, instead it's required to use "state value" representation like this - `machine.transition({ a: 'b' }, 'NEXT')`.

- [`025a2d6`](https://github.com/davidkpiano/xstate/commit/025a2d6a295359a746bee6ffc2953ccc51a6aaad) [#898](https://github.com/davidkpiano/xstate/pull/898) Thanks [@davidkpiano](https://github.com/davidkpiano)! - - Breaking: activities removed (can be invoked)

  Since activities can be considered invoked services, they can be implemented as such. Activities are services that do not send any events back to the parent machine, nor do they receive any events, other than a "stop" signal when the parent changes to a state where the activity is no longer active. This is modeled the same way as a callback service is modeled.

* [`e09efc7`](https://github.com/davidkpiano/xstate/commit/e09efc720f05246b692d0fdf17cf5d8ac0344ee6) [#878](https://github.com/davidkpiano/xstate/pull/878) Thanks [@Andarist](https://github.com/Andarist)! - Removed previously deprecated config properties: `onEntry`, `onExit`, `parallel` and `forward`.

- [`31a0d89`](https://github.com/davidkpiano/xstate/commit/31a0d890f55d8f0b06772c9fd510b18302b76ebb) [#1002](https://github.com/davidkpiano/xstate/pull/1002) Thanks [@Andarist](https://github.com/Andarist)! - Removed support for `service.send(type, payload)`. We are using `send` API at multiple places and this was the only one supporting this shape of parameters. Additionally, it had not strict TS types and using it was unsafe (type-wise).

## 4.7.8

### Patch Changes

- [`520580b`](https://github.com/davidkpiano/xstate/commit/520580b4af597f7c83c329757ae972278c2d4494) [#967](https://github.com/davidkpiano/xstate/pull/967) Thanks [@andrewgordstewart](https://github.com/andrewgordstewart)! - Add context & event types to InvokeConfig

## 4.7.7

### Patch Changes

- [`c8db035`](https://github.com/davidkpiano/xstate/commit/c8db035b90a7ab4a557359d493d3dd7973dacbdd) [#936](https://github.com/davidkpiano/xstate/pull/936) Thanks [@davidkpiano](https://github.com/davidkpiano)! - The `escalate()` action can now take in an expression, which will be evaluated against the `context`, `event`, and `meta` to return the error data.

* [`2a3fea1`](https://github.com/davidkpiano/xstate/commit/2a3fea18dcd5be18880ad64007d44947cc327d0d) [#952](https://github.com/davidkpiano/xstate/pull/952) Thanks [@davidkpiano](https://github.com/davidkpiano)! - The typings for the raise() action have been fixed to allow any event to be raised. This typed behavior will be refined in version 5, to limit raised events to those that the machine accepts.

- [`f86d419`](https://github.com/davidkpiano/xstate/commit/f86d41979ed108e2ac4df63299fc16f798da69f7) [#957](https://github.com/davidkpiano/xstate/pull/957) Thanks [@Andarist](https://github.com/Andarist)! - Fixed memory leak - each created service has been registered in internal map but it was never removed from it. Registration has been moved to a point where Interpreter is being started and it's deregistered when it is being stopped.

## 4.7.6

### Patch Changes

- dae8818: Typestates are now propagated to interpreted services.

## 4.7.5

### Patch Changes

- 6b3d767: Fixed issue with delayed transitions scheduling a delayed event for each transition defined for a single delay.

## 4.7.4

### Patch Changes

- 9b043cd: The initial state is now cached inside of the service instance instead of the machine, which was the previous (faulty) strategy. This will prevent entry actions on initial states from being called more than once, which is important for ensuring that actors are not spawned more than once.

## 4.7.3

### Patch Changes

- 2b134eee: Fixed issue with events being forwarded to children after being processed by the current machine. Events are now always forwarded first.
- 2b134eee: Fixed issue with not being able to spawn an actor when processing an event batch.
