### Conditionals overview

Handlers are tasks that only run if triggered by a task that has changed something.

Items can be used to iterate over a list.

When is used for conditional task execution.

Blocks can be used to implement if-then-else like statements

Register is useful aid to record the result of a task in a variable.

Fail is a module that is used to perform a specific task on failure.



### Understanding handlers

A handler is a task that is only executed when triggered by a task that has changed something.

Handlers are executed after all tasks in a play.

If any task fails after the task that has called the handler, handlers are not executed.

To change that behavior
```
force_handlers
```

Default behavior is ansible happens to be if execution of a specific task, on a specific host is failing then further play execution on that host is aborted.

