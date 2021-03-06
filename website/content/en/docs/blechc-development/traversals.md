
## Recursive traversals that do similar things


<!-- ## Recursive traversals on typed Blech data structures -->

### TypeChecking.fs
#### Traversing statements only

```
/// Determine the return type of a statement
let rec private stmtType stmt
```

```
/// An activity must have some synchronous delay statement on every possible 
/// execution path through its body.
/// This can be an await or a run statement.
// This is partially double work with causality analysis, 
// here and there we will find instantaneous loops.
// Here we throw an error if that is the only statement in the activity.
// In causality analysis we throw an error any way.
let private checkStmtsWillPause p name stmts
```

```
let private determineGlobalOutputs lut bodyRes
```

#### Traversing expressions and statements
```
/// Checks whether the lists of statements does not contain any synchronous
/// statements. This check is required for function bodies.
let private checkAbsenceOfSyncStmts stmts
```

```
let private determineCalledSingletons lut bodyRes =
```

### TyChkExpressions.fs
#### Traversing expressions only

```
/// Returns true when the evaluation of expr does not change the program's state
let rec private hasNoSideEffect expr
```

```
/// True if given expression contains only compile time or param values
let rec internal isStaticExpr lut expr
```

```
/// Given a typed rhs expression, this function tries to evaluate this 
/// expression to a constant and return a new TypedRhs such that
/// isLiteral returns true on that.
/// However, it may return a non-constant expression if it cannot be
/// reduced. No error is thrown.
let rec internal tryEvalConst lut (checkedExpr: TypedRhs) : TypedRhs
```

### TyChkAmendment.fs
#### Traversing expressions only

```
/// Returns yes if the expression contains at least one name
/// This is useful for type checking where we want to make sure that
/// in a variable declaration such as "var x = 8", we do not merely
/// look at the type of "8" which is int8 and falsely make x an int8
/// as well but instead give an error to the user.
let rec private exprContainsName rhs
```

### ProgramGraph.fs
#### Traversing expressions only

```
let rec internal addNameRead context node trhs =
```

```
/// This function is here temporarily. It solves the following problem:
/// Since the abort conditions are pushed into the body of the abort statement
/// conditions may become concurrent in the context of a cobegin in the body.
/// This is a problem if the condition is modified in the body. The scheduler
/// will generate wrong code following the write first-then read strategy.
/// To avoid that we prev all variables which are part of the abort condition
/// and which are modified inside the abort body.
/// In the future we want to generate code for preemptions differently
/// such that prev'ing conditions won't be necessary.
/// This future implementation would introduce another control point which is
/// traversed in every reaction before executing any blocks that correspond to
/// the abort body.
/// The future solution would allow to use references in cond which cannot be prev'ed.
/// As of right now references are not implemented and thus the 
/// current implementation always works.
let private tempPrevHelper context cond subPg
```

#### Traversing statements only

```
/// Generates a program graph for a given statement
let rec private createPGofStmt context thread stmt
```

### CPdataAccess.fs
#### Traversing expressions only

```
/// Convert expressions into Doc
/// Ensures that function calls with struct literals in their input list are
/// converted to function calls on precomputed structure variables.
// The result propagates recursively.
// Even if the resulting data type is simple, the expression producing that
// may contain sub-expressions that need to be rewritten, e.g.
// g( f({i=8}) ), where f is simply boolean but its argument needs to be rewritten
and private cpExpr inFunction ctx expr
```

### ActivityTranslator.fs
#### Traversing expressions only

```
/// Extract all QNames of variables that require a prev location in given code
// runs on nodes and program graphs instead of statements
// became necessary when strong abort was translated by introducing a prev on the condition and thus deviating from given source code
// might change again
let private collectVarsToPrev2 pg
```

### FunctionTranslator.fs
#### Traversing statements only

```
and private translateFunctionStatement ctx curComp stmt
```

