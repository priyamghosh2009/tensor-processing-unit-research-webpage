# Tensor MAX ISA Specification

> **Tensor MAX Instruction Set Architecture — Technical Specification**

**Status:** Experimental / Research

**Version:** 0.1

**Author:** Priyam Ghosh

**Target:** Tensor Processing and AI Acceleration

---

# 1. Introduction

Tensor MAX is a tensor-native Instruction Set Architecture designed for specialized processors capable of operating directly on tensor data.

The architecture defines a set of instructions for:

- Tensor creation
- Tensor assignment
- Tensor arithmetic
- Matrix multiplication
- Tensor transformation
- Broadcasting
- Forward tensor operations
- Transpose Integration
- Mathematical functions
- Data-type control
- Tensor timing

This document defines the current conceptual architecture and instruction semantics.

The specification is experimental and may change as the architecture evolves.

---

# 2. Architectural Model

Tensor MAX treats tensors as first-class architectural objects.

A tensor may be represented as:


$\ T \in \mathbb{D}^{d_1 \times d_2 \times \dots \times d_n} \$

where:

- $\ T \$ is a tensor
- $\ \mathbb{D} \$ is the tensor data type
- $\ d_i \$ represents the size of tensor dimension \(i\)
- $\ n \$ is the tensor rank

Examples include:

### Scalar

$\ T \in \mathbb{D}\$

### Vector

$\ T \in \mathbb{D}^{n} \$

### Matrix

$\ T \in \mathbb{D}^{m \times n} \$

### Higher-dimensional tensor

$\ T \in \mathbb{D}^{d_1 \times d_2 \times d_3} \$

---

# 3. Tensor Registers

Tensor registers use the `T` prefix.

Examples:

```text
T1
T2
T3
T4
```

A tensor register can contain tensor data together with architectural metadata such as:

- Tensor shape
- Tensor rank
- Data type
- Memory location
- Layout information

The exact register-file implementation is implementation-dependent.

---

# 4. Instruction Syntax

The general Tensor MAX syntax is:

```text
INSTRUCTION OPERAND1,OPERAND2,...
```

Examples:

```text
TADD T1,T2
TASSIGN T1,[[900,900],[200,300]]
TCREATE T3
TDTYPE T1,INT64
```

Whitespace between operands is optional according to the assembler syntax.

---

# 5. Instruction Categories

Tensor MAX instructions are grouped into:

| Category | Instructions |
|---|---|
| Tensor Management | `TCREATE`, `TASSIGN` |
| Arithmetic | `TADD`, `TSUB`, `TMULX`, `TDIV` |
| Matrix | `TMMUL` |
| Transformation | `TTSP`, `TBDC` |
| Tensor Processing | `TFWD`, `TINT` |
| Mathematics | `TSIN`, `TASIN`, `TCOS`, `TACOS`, `TTAN`, `TATAN`, `TCOT`, `TACOT`, `TSEC`, `TASEC`, `TCOSEC`,`TACOSEC`  |
| Data Types | `TDTYPE` |
| Timing | `TCLOCK` |

---

# 6. Instruction Definitions

## 6.1 `TCREATE`

### Syntax

```text
TCREATE Tn
```

### Description

Creates or initializes a tensor register.

### Example

```text
TCREATE T1
TCRAETE T2
```

### Operands

| Operand | Description |
|---|---|
| `Tn` | Destination tensor register |

### Conceptual Operation

\[
T_n \leftarrow \varnothing
\]

The exact initialization state is implementation-defined.

---

## 6.2 `TASSIGN`

### Syntax

```text
TASSIGN Tn,DATA
```

### Description

Assigns tensor data to a tensor register.

### Example

```text
TASSIGN T1,[[900,900],[200,300]]
```

### Conceptual Tensor

\[
T_1 =
\begin{bmatrix}
900 & 900\\
200 & 300
\end{bmatrix}
\]

### Operands

| Operand | Description |
|---|---|
| `Tn` | Destination tensor |
| `DATA` | Tensor literal or supported data source |

---

## 6.3 `TADD`

### Syntax

```text
TADD T1,T2
```

### Description

Adds two compatible tensors.

### Conceptual Operation

\[
T_1 \leftarrow T_1 + T_2
\]

### Shape Requirement

For element-wise addition:

\[
\operatorname{shape}(T_1)=\operatorname{shape}(T_2)
\]

or the operands must satisfy the Tensor MAX broadcasting rules.

---

## 6.4 `TSUB`

### Syntax

```text
TSUB T1,T2
```

### Description

Subtracts `T2` from `T1`.

### Conceptual Operation

\[
T_1 \leftarrow T_1 - T_2
\]

---

## 6.5 `TMULX`

### Syntax

```text
TMULX T1,T2
```

### Description

Performs the Tensor MAX tensor multiplication operation.

### Conceptual Operation

\[
T_1 \leftarrow T_1 \otimes T_2
\]

The exact multiplication operator and shape rules are defined by the final Tensor MAX tensor execution model.

---

## 6.6 `TDIV`

### Syntax

```text
TDIV T1
```

### Description

Performs the Tensor MAX tensor division operation.

### Conceptual Operation

\[
T_1 \leftarrow \operatorname{DIV}(T_1)
\]

The exact operand and division semantics remain subject to final ISA definition.

---

## 6.7 `TMMUL`

### Syntax

```text
TMMUL T1,T2
```

### Description

Performs matrix multiplication.

### Conceptual Operation

\[
T_1 \leftarrow T_1T_2
\]

For matrices:

\[
A \in \mathbb{D}^{m\times k}
\]

and:

\[
B \in \mathbb{D}^{k\times n}
\]

the result is:

\[
C=AB
\]

where:

\[
C\in\mathbb{D}^{m\times n}
\]

### Example

```text
T1 = [[1,2],
      [3,4]]

T2 = [[5,6],
      [7,8]]
```

```text
TMMUL T1,T2
```

Result:

```text
T1 = [[19,22],
      [43,50]]
```

---

## 6.8 `TTSP`

### Syntax

```text
TTSP T1
```

### Description

Performs a tensor transpose operation.

For a two-dimensional tensor:

\[
T' = T^\top
\]

### Example

```text
T1 =
[[1,2,3],
 [4,5,6]]
```

After:

```text
TTSP T1
```

```text
T1 =
[[1,4],
 [2,5],
 [3,6]]
```

---

## 6.9 `TBPG`

### Syntax

```text
TBPG T1
```

### Description

Performs Tensor MAX broadcasting.

Broadcasting allows tensor dimensions to be aligned or expanded according to the Tensor MAX shape compatibility rules.

The final broadcasting rules are part of the architectural specification under development.

---

## 6.10 `TFWD`

### Syntax

```text
TFWD T1
```

### Description

Performs a Tensor MAX forward tensor operation.

The instruction is intended to provide a native primitive for forward tensor processing and may be applicable to AI and neural-network computation.

The exact operation is implementation-defined until formally specified.

---

## 6.11 `TINT`

### Syntax

```text
TINT T1,x
```

### Name

**Transpose Integration**

### Description

Performs the Tensor MAX Transpose Integration operation.

Conceptually, the operation combines a transpose transformation with an integration-related tensor operation.

A conceptual mathematical representation is:

\[
T' = \int T^\top\,dx
\]

where `x` represents the transpose/integration parameter.

### Operand

| Operand | Description |
|---|---|
| `T1` | Tensor being processed |
| `x` | Transpose Integration parameter |

### Status

The exact mathematical definition, axis semantics, output shape, and numerical integration method are **not yet finalized**.

Potential future definitions may specify:

- Integration axis
- Transpose axis permutation
- Numerical integration method
- Output shape
- Precision behavior

---

# 7. Mathematical Instructions

## 7.1 `TSIN`

```text
TSIN T1
```

Computes the sine function over tensor data.

\[
T_1 \leftarrow \sin(T_1)
\]

---

## 7.2 `TASIN`

```text
TASIN T1
```

Computes inverse sine.

\[
T_1 \leftarrow \arcsin(T_1)
\]

---

## 7.3 `TCOS`

```text
TCOS T1
```

Computes cosine.

\[
T_1 \leftarrow \cos(T_1)
\]

---

## 7.4 `TACOS`

```text
TACOS T1
```

Computes inverse cosine.

\[
T_1 \leftarrow \arccos(T_1)
\]

---

## 7.5 `TTAN`

```text
TTAN T1
```

Computes tangent.

\[
T_1 \leftarrow \tan(T_1)
\]

---

## 7.6 `TATAN`

```text
TATAN T1
```

Computes inverse tangent.

\[
T_1 \leftarrow \arctan(T_1)
\]

---

## 7.7 `TCOT`

```text
TCOT T1
```

Computes cotangent.

\[
T_1 \leftarrow \cot(T_1)
\]

---

# 8. Data-Type Instruction

## `TDTYPE`

### Syntax

```text
TDTYPE T1,INT64
```

### Description

Sets or changes the data type associated with a tensor.

### Example

```text
TDTYPE T1,INT64
```

### Potential Data Types

```text
INT8
INT16
INT32
INT64

FP16
BF16
FP32
FP64
```

The final supported type list is subject to architectural definition.

---

# 9. Tensor Clock

## `TCLOCK`

### Syntax

```text
TCLOCK T1
```

### Description

Provides tensor clock or timing functionality.

Possible architectural applications include:

- Tensor execution timing
- Performance measurement
- Hardware cycle measurement
- Synchronization
- Profiling

The exact semantics of `TCLOCK` are not yet finalized.

---

# 10. Shape and Compatibility

Tensor operations may require compatible shapes.

For example:

\[
A+B
\]

requires compatible dimensions under the Tensor MAX broadcasting rules.

Matrix multiplication requires:

\[
A\in\mathbb{D}^{m\times k}
\]

and:

\[
B\in\mathbb{D}^{k\times n}
\]

producing:

\[
C\in\mathbb{D}^{m\times n}
\]

Shape checking may be performed by:

- Hardware
- Runtime
- Compiler
- Assembler
- A combination of these components

The final behavior is to be defined.

---

# 11. Data Representation

Tensor MAX tensors may contain different numerical representations.

Potential formats include:

```text
INT8
INT16
INT32
INT64

FP16
BF16
FP32
FP64
```

Future versions may define:

- Custom precision
- Quantized formats
- Block floating point
- Sparse tensor formats
- Complex numbers
- Custom AI formats

---

# 12. Instruction Summary

```text
TCREATE T3
TASSIGN T1,[[900,900],[200,300]]

TADD T1,T2
TSUB T1,T2
TMULX T1,T2
TDIV T1

TMMUL T1,T2

TTSP T1
TBPG T1
TFWD T1
TINT T1,x

TSIN T1
TASIN T1
TCOS T1
TACOS T1
TTAN T1
TATAN T1
TCOT T1

TDTYPE T1,INT64

TCLOCK T1
```

---

# 13. Example Program

```text
TCREATE T1
TCREATE T2
TCREATE T3

TASSIGN T1,[[1,2],[3,4]]
TASSIGN T2,[[5,6],[7,8]]

TADD T1,T2

TMMUL T1,T2

TTSP T1

TBPG T1

TFWD T1

TSIN T1

TDTYPE T1,INT64

TCLOCK T1
```

---

# 14. Future ISA Work

The following areas remain under development:

- Formal register architecture
- Tensor register file
- Tensor shape metadata
- Instruction encoding
- Opcode allocation
- Immediate values
- Memory addressing
- Tensor memory layout
- Tensor load/store instructions
- Tensor data types
- Precision rules
- Broadcasting semantics
- `TINT` formal semantics
- `TFWD` formal semantics
- `TBPG` formal semantics
- `TCLOCK` formal semantics
- Exception handling
- Hardware execution model
- Compiler integration
- Assembler syntax
- Simulator implementation
- RTL implementation
- Verification methodology

---

# 15. Specification Status

This document represents the **current experimental Tensor MAX ISA definition**.

Instructions and semantics that are explicitly marked as experimental or undefined should not be considered finalized.

The architecture is expected to evolve as Tensor MAX research progresses.

---

# 16. Author

**Priyam Ghosh**

Tensor MAX ISA Research

Research interests include:

- Instruction Set Architecture
- Tensor Processing Units
- Computer Architecture
- AI Accelerators
- Machine Learning Systems
- Foundation Models
- High-Performance Computing
- Hardware/Software Co-design

---

> **Tensor MAX — A tensor-native ISA for tensor-native computing.**
