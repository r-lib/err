
# Specification of r-lib errors, version 1.0.0

Note: this is an experimental draft.

Note: version 1.0.0 corresponds to the `rlang_error` class, as
implemented in rlang 0.4.0.

## Classes

An r-lib error object always has at least classes `rlib_error`, `error`
and `condition`. It may have other classes.

## Members

An `rlib_error` object has the following members:

* `message`: `character(1)`, optional, the error message. If this is
  missing then the `conditionMessage()` S3 method must be able to generate
  the error message for the class.
* `call`: optional, the captured call, of mode `call`.
  Even if this is missing, the `conditionCall()` S3 method might still
  generate the call.
* `trace`: optional, an `rlib_trace` object, see below. 
* `parent`: optional, pointer to a parent error, of class `error`. It
  may also be `NULL`, which is the same as missing.
* `version`: optional, `rlib_error` version, defaults to 1.0.0, i.e. this
  specification.

## Traces

An `rlib_trace` object has the following members:

* `calls`: A list of calls, each of mode `call`. This is the stack when
  the error was thrown. `calls[[1]]` is the top of the stack.
* `parents`: Integer vector of parent frame pointers, as in `sys.parents()`.
* `envs`: List or character vector that contains the ids of the execution
   environments of the stack.
* `indices`: Frame numbers, typically just 1:n.
* `version`: options, `r_lib` error version, defaults to 1.0.0, i.e. this
  specification.

`calls`, `parents`, `envs` and `indices` must have the same length.

## Parent errors

Parent errors may be arbitrary error objects. A parent error may also
be another `rlib_error`, that includes another trace, etc.

If the error has a parent error (i.e. `parent` is not `NULL`), then
the trace of the error may only include part of the stack: from the top to
the place where the error was thrown. The same holds for the parent error.
(This is the rlang 0.4.0 behavior.)

## Versioning

Future versions of the `rlib_error` and `rlib_trace` classes will
always add a `version` member.

### Backward compatibility

Whenever new versions of the `rlib_error` and `rlib_trace` classes
introduce backward incompatible changes, the major version number is
increased.
