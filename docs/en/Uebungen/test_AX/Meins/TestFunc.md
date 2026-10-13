# TestFunc: Function (IEC 61499 Function)

![TestFunc](./TestFunc.svg)

* * * * * * * * * *

## Introduction

`TestFunc` is not a SubApp but a function type (`.fct`, standard 61499-1) in the package `Meins` with the comment "Function with empty body". It serves as a simple test object for the function syntax: one event input `REQ`, one event output `CNF` and a Structured Text body. There are no function block instances and no connections.

## Function Interface

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `REQ` | `Event (event input)` | - | Execution request. |
| `CNF` | `Event (event output)` | `With` = `X` (and one unnamed variable) | Confirmation; delivers the two output values. |
| `X` | `INT (output variable)` | initial value `5` | Output parameter, set to `7` in the body. |
| (unnamed) | `INT (output variable)` | - | Second output variable with an empty name; corresponds to the return value `TestFunc`. |

## Program Flow and Connections

The body is written in Structured Text:

```
PACKAGE Meins;

FUNCTION TestFunc : INT

VAR_OUTPUT
	X : INT := 5;
END_VAR

TestFunc := 5;
X := 7;
END_FUNCTION
```

1. The return value `TestFunc` (INT) is set to `5`.
2. The output parameter `X` (initial value `5`) is set to `7`.

Although the comment says "empty body", the body contains these two assignments.

## Summary

`TestFunc` is a minimal example of an IEC 61499 function with an event interface, a return value and one output parameter.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
