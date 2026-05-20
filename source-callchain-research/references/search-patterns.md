# Search Patterns

Use this reference only when simple direct-call search is not enough.

## Fast Entry Discovery

Start with one or more of these:

- `rg "symbol_name\\("`
- `rg "class_name|struct_name|enum_name"`
- `rg "log text here"`
- `rg "config_key|flag_name"`
- `rg "register|dispatch|submit|enqueue|schedule|execute|run"`

For file discovery, prefer:

- `rg --files | rg "scheduler|executor|worker|dispatch|router|binding"`

## Python Patterns

Look for:

- `def foo(`
- `self.foo(`
- `getattr(..., "foo")`
- decorator registration
- `async def` / `await`
- `Thread`, `Process`, `Pool`, `Executor`

Watch for:

- monkey patching
- dynamic imports
- factories that return different implementations

## C/C++ Patterns

Look for:

- direct calls: `foo(`
- virtual dispatch: `virtual`, `override`
- function pointers and tables
- registration macros
- constructors that wire callbacks
- namespaces that hide the real implementation file

Watch for:

- inline wrappers in headers
- macro-expanded dispatch
- template instantiation hiding the real call edge

## Mixed-Language Patterns

When Python and C/C++ interact, search for:

- `pybind11`, `PYBIND11_MODULE`
- `PyMethodDef`, `PyObject*`
- `ctypes`, `cffi`, `cython`
- module names shared between Python import path and native source

Prove both sides:

1. where Python prepares the call
2. where native code receives it
3. where native code continues afterward

## Async and Worker Boundaries

Distinguish these edges:

- producer side: `submit`, `enqueue`, `post`, `schedule`
- consumer side: `run`, `loop`, `worker`, `poll`, `handle`

Do not claim `submit_x()` directly executes `handle_x()` unless the code proves it.

## Good Final Questions

Before finalizing, check:

- Did I prove the next hop, or only find a similarly named symbol?
- Did I separate registration from execution?
- Did I identify the branch condition that selects this path?
- Did I mark thread/process/language boundaries?
- Did I stop at the boundary the user actually asked about?
