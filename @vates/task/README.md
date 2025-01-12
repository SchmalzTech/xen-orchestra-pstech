<!-- DO NOT EDIT MANUALLY, THIS FILE HAS BEEN GENERATED -->

# @vates/task

[![Package Version](https://badgen.net/npm/v/@vates/task)](https://npmjs.org/package/@vates/task) ![License](https://badgen.net/npm/license/@vates/task) [![PackagePhobia](https://badgen.net/bundlephobia/minzip/@vates/task)](https://bundlephobia.com/result?p=@vates/task) [![Node compatibility](https://badgen.net/npm/node/@vates/task)](https://npmjs.org/package/@vates/task)

## Install

Installation of the [npm package](https://npmjs.org/package/@vates/task):

```sh
npm install --save @vates/task
```

## Usage

```js
import { Task } from '@vates/task'

const task = new Task({
  // data in this object will be sent along the *start* event
  //
  // property names should be chosen as not to clash with properties used by `Task` or `combineEvents`
  data: {
    name: 'my task',
  },

  // if defined, a new detached task is created
  //
  // if not defined and created inside an existing task, the new task is considered a subtask
  onProgress(event) {
    // this function is called each time this task or one of it's subtasks change state
    const { id, timestamp, type } = event
    if (type === 'start') {
      const { name, parentId } = event
    } else if (type === 'end') {
      const { result, status } = event
    } else if (type === 'info' || type === 'warning') {
      const { data, message } = event
    } else if (type === 'property') {
      const { name, value } = event
    }
  },
})

// this field is settable once before being observed
task.id

// contains the current status of the task
//
// possible statuses are:
// - pending
// - success
// - failure
// - aborted
task.status

// Triggers the abort signal associated to the task.
//
// This simply requests the task to abort, it will be up to the task to handle or not this signal.
task.abort(reason)

// if fn rejects, the task will be marked as failed
const result = await task.runInside(fn)

// if fn rejects, the task will be marked as failed
// if fn resolves, the task will be marked as succeeded
const result = await task.run(fn)
```

Inside a task:

```js
// the abort signal of the current task if any, otherwise is `undefined`
Task.abortSignal

// sends an info on the current task if any, otherwise does nothing
Task.info(message, data)

// sends an info on the current task if any, otherwise does nothing
Task.warning(message, data)

// attaches a property to the current task if any, otherwise does nothing
//
// the latest value takes precedence
//
// examples:
// - progress
Task.set(property, value)
```

### `combineEvents`

Create a consolidated log from individual events.

It can be used directly as an `onProgress` callback:

```js
import { makeOnProgress } from '@vates/task/combineEvents'

const onProgress = makeOnProgress({
  // This function is called each time a root task starts.
  //
  // It will be called for as many times as there are tasks created with this `onProgress` function.
  onRootTaskStart(taskLog) {
    // `taskLog` is an object reflecting the state of this task and all its subtasks,
    // and will be mutated in real-time to reflect the changes of the task.
  },

  // This function is called each time a root task ends.
  onRootTaskEnd(taskLog) {},

  // This function is called each time a root task or a subtask is updated.
  //
  // `taskLog.$root` can be used to uncondionally access the root task.
  onTaskUpdate(taskLog) {},
})

Task.run({ data: { name: 'my task' }, onProgress }, asyncFn)
```

It can also be fed event logs directly:

```js
import { makeOnProgress } from '@vates/task/combineEvents'

const onProgress = makeOnProgress({ onRootTaskStart, onRootTaskEnd, onTaskUpdate })

eventLogs.forEach(onProgress)
```

## Contributions

Contributions are _very_ welcomed, either on the documentation or on
the code.

You may:

- report any [issue](https://github.com/vatesfr/xen-orchestra/issues)
  you've encountered;
- fork and create a pull request.

## License

[ISC](https://spdx.org/licenses/ISC) © [Vates SAS](https://vates.fr)
