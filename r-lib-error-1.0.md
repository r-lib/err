
# Specification of r-lib errors, version 1.0.0

Note: this is an experimental draft.

Note: version 1.0.0 corresponds to the `rlang_error` class, as
implemented in rlang 0.4.1.

## The `rlib_error` object

### Classes

An r-lib error object always has at least classes `rlib_error`, `error`
and `condition`, in this order. It may have other subclasses, i.e. other
classes must precede `rlib_error`.

An `rlib_error` object is a named list.

### Fields

An `rlib_error` object has the following fields:

* `message`: `character(1)`, optional, the error message. If this is
  missing then the `conditionMessage()` S3 method must be able to generate
  the error message for the class. This conforms to the API of base R
  `condition` objects.
* `call`: optional, the captured call, of mode `call`.
  Even if this is missing, the `conditionCall()` S3 method might still
  generate the call. This conforms to the API of base R `condition`
  objects.
* `trace`: optional, an `rlib_trace` object, see below.
* `parent`: optional, pointer to a parent error, of class `error`. It
  may also be `NULL`, which is the same as missing.
* `version`: optional, `rlib_error` version, defaults to 1.0.0, i.e. this
  specification.

## Traces

An `rlib_trace` object has the following fields:

* `calls`: A list of calls, each of mode `call`. This is the stack when
  the error was thrown. `calls[[1]]` is the top of the stack.
* `parents`: Integer vector of parent frame pointers, as in `sys.parents()`.
* `ids`: Character vector that contains the ids of the execution
  environments of the stack. In rlang 0.4.1 these ids are the hexadecimal
  addresses of the execution environments, e.g. `0x7fb3d335c840`, but this
  is not requirement, and it might change in the future.
* `indices`: Frame numbers, typically just 1:n.
* `version`: options, `r_lib` error version, defaults to 1.0.0, i.e. this
  specification.

`calls`, `parents`, `envs` and `indices` must have the same length.

## Parent errors

Parent errors may be arbitrary error objects. A parent error may also
be another `rlib_error`, that includes another trace, etc.

If the error has a parent error (i.e. `parent` is not `NULL`), then
the frames of the traces of the parent and child errors may overlap at
the top of the stack. (This is the rlang 0.4.1 behavior.)

## Versioning

Future versions of the `rlib_error` and `rlib_trace` classes will
always add a `version` member.

### Backward compatibility

Whenever new versions of the `rlib_error` and `rlib_trace` classes
introduce backward incompatible changes, the major version number is
increased.
