# Sexy Clang-Tidy

This project contains a Clang-Tidy configuration which I deem reasonable for my projects.

## Configuration rationale

1. We start out with CLion's default Clang-Tidy configuration. For reference, these are the checks which are enabled by
   default:

   ```
   -*
   bugprone-argument-comment
   bugprone-assert-side-effect
   bugprone-bad-signal-to-kill-thread
   bugprone-branch-clone
   bugprone-copy-constructor-init
   bugprone-dangling-handle
   bugprone-dynamic-static-initializers
   bugprone-fold-init-type
   bugprone-forward-declaration-namespace
   bugprone-forwarding-reference-overload
   bugprone-inaccurate-erase
   bugprone-incorrect-roundings
   bugprone-integer-division
   bugprone-lambda-function-name
   bugprone-macro-parentheses
   bugprone-macro-repeated-side-effects
   bugprone-misplaced-operator-in-strlen-in-alloc
   bugprone-misplaced-pointer-arithmetic-in-alloc
   bugprone-misplaced-widening-cast
   bugprone-move-forwarding-reference
   bugprone-multiple-statement-macro
   bugprone-no-escape
   bugprone-not-null-terminated-result
   bugprone-parent-virtual-call
   bugprone-posix-return
   bugprone-reserved-identifier
   bugprone-sizeof-container
   bugprone-sizeof-expression
   bugprone-spuriously-wake-up-functions
   bugprone-string-constructor
   bugprone-string-integer-assignment
   bugprone-string-literal-with-embedded-nul
   bugprone-suspicious-enum-usage
   bugprone-suspicious-include
   bugprone-suspicious-memset-usage
   bugprone-suspicious-missing-comma
   bugprone-suspicious-semicolon
   bugprone-suspicious-string-compare
   bugprone-suspicious-memory-comparison
   bugprone-suspicious-realloc-usage
   bugprone-swapped-arguments
   bugprone-terminating-continue
   bugprone-throw-keyword-missing
   bugprone-too-small-loop-variable
   bugprone-undefined-memory-manipulation
   bugprone-undelegated-constructor
   bugprone-unhandled-self-assignment
   bugprone-unused-raii
   bugprone-unused-return-value
   bugprone-use-after-move
   bugprone-virtual-near-miss
   cert-dcl21-cpp
   cert-dcl58-cpp
   cert-err34-c
   cert-err52-cpp
   cert-err60-cpp
   cert-flp30-c
   cert-msc50-cpp
   cert-msc51-cpp
   cert-str34-c
   cppcoreguidelines-interfaces-global-init
   cppcoreguidelines-narrowing-conversions
   cppcoreguidelines-pro-type-member-init
   cppcoreguidelines-pro-type-static-cast-downcast
   cppcoreguidelines-slicing
   google-default-arguments
   google-explicit-constructor
   google-runtime-operator
   hicpp-exception-baseclass
   hicpp-multiway-paths-covered
   misc-misplaced-const
   misc-new-delete-overloads
   misc-no-recursion
   misc-non-copyable-objects
   misc-throw-by-value-catch-by-reference
   misc-unconventional-assign-operator
   misc-uniqueptr-reset-release
   modernize-avoid-bind
   modernize-concat-nested-namespaces
   modernize-deprecated-headers
   modernize-deprecated-ios-base-aliases
   modernize-loop-convert
   modernize-make-shared
   modernize-make-unique
   modernize-pass-by-value
   modernize-raw-string-literal
   modernize-redundant-void-arg
   modernize-replace-auto-ptr
   modernize-replace-disallow-copy-and-assign-macro
   modernize-replace-random-shuffle
   modernize-return-braced-init-list
   modernize-shrink-to-fit
   modernize-unary-static-assert
   modernize-use-auto
   modernize-use-bool-literals
   modernize-use-emplace
   modernize-use-equals-default
   modernize-use-equals-delete
   modernize-use-nodiscard
   modernize-use-noexcept
   modernize-use-nullptr
   modernize-use-override
   modernize-use-transparent-functors
   modernize-use-uncaught-exceptions
   mpi-buffer-deref
   mpi-type-mismatch
   openmp-use-default-none
   performance-faster-string-find
   performance-for-range-copy
   performance-implicit-conversion-in-loop
   performance-inefficient-algorithm
   performance-inefficient-string-concatenation
   performance-inefficient-vector-operation
   performance-move-const-arg
   performance-move-constructor-init
   performance-no-automatic-move
   performance-noexcept-move-constructor
   performance-trivially-destructible
   performance-type-promotion-in-math-fn
   performance-unnecessary-copy-initialization
   performance-unnecessary-value-param
   portability-simd-intrinsics
   readability-avoid-const-params-in-decls
   readability-const-return-type
   readability-container-size-empty
   readability-convert-member-functions-to-static
   readability-delete-null-pointer
   readability-deleted-default
   readability-inconsistent-declaration-parameter-name
   readability-make-member-function-const
   readability-misleading-indentation
   readability-misplaced-array-index
   readability-non-const-parameter
   readability-redundant-control-flow
   readability-redundant-declaration
   readability-redundant-function-ptr-dereference
   readability-redundant-smartptr-get
   readability-redundant-string-cstr
   readability-redundant-string-init
   readability-simplify-subscript-expr
   readability-static-accessed-through-instance
   readability-static-definition-in-anonymous-namespace
   readability-string-compare
   readability-uniqueptr-delete-release
   readability-use-anyofallof
   ```

2. We disable vendor- and application-specific checks: I don't think that a default linting configuration should depend
   on any particular style.

   The following list describes the checks to be disabled:

   * `cert-*`;
   * `google-*`;
   * `hicpp-*`;
   * `mpi-*`;
   * `openmp-*`.

   Clang-Tidy has a huge amount of checks; it is unrealistic to go through them all. We'll assume that all other checks
   enabled by CLion's default configuration are adequate.

3. We enable all `clang-analyzer-*` checks: this should be a reasonable default set of options to be enabled. Further,
   given that Boost is frequently used, we enable `boost-*` checks.

4. We selectively enable a few more checks; the general process behind choosing which checks to enable is the following:

   1. If a check requires changes to improve readability or correctness _without_ affecting the semantics of the
      program, it gets enabled. For example, this implies that const-correctness checks get enabled.

   2. A decent programming experience is assumed. Developers should not be expected to avoid using language features
      just because they can be used inadequately. For example, this implies that checks such as
      `cppcoreguidelines-avoid-do-while` and `cppcoreguidelines-avoid-goto` remain disabled.

   3. Working on real, non-toy programs is assumed. Among others, this includes potentially unsafe constructs such as
      `const_cast` and `reinterpret_cast`, or global non-const variables. For example, this implies that checks such as
      `cppcoreguidelines-avoid-non-const-global-variables` remain disabled.

   4. Style is _not_ enforced on programmers. If the same code can be written in two equivalent ways, neither of the two
      possibilities needs to be enforced by any particular check. For example, this implies that
      `modernize-use-trailing-return-type` remains disabled.

   5. If it is possible to modernize the code without violating any of the rules defined above, the corresponding check
      gets enabled.

   Checks enabled due to this step are:

   * `bugprone-bool-pointer-implicit-conversion` (i);
   * `bugprone-exception-escape` (i);
   * `bugprone-implicit-widening-of-multiplication-result` (i);
   * `bugprone-infinite-loop` (i);
   * `bugprone-multiple-new-in-one-expression` (i);
   * `bugprone-narrowing-conversions` (i);
   * `bugprone-non-zero-enum-to-bool-conversion` (i);
   * `bugprone-redundant-branch-condition` (i);
   * `bugprone-shared-ptr-array-mismatch` (i);
   * `bugprone-signal-handler` (i);
   * `bugprone-signed-char-misuse` (i);
   * `bugprone-standalone-empty` (i);
   * `bugprone-stringview-nullptr` (i);
   * `bugprone-unchecked-optional-access` (i);
   * `bugprone-unhandled-exception-at-new` (i);
   * `bugprone-unsafe-functions` (i);
   * `concurrency-*` (i);
   * `cppcoreguidelines-avoid-capturing-lambda-coroutines` (i);
   * `cppcoreguidelines-avoid-const-or-ref-data-members` (i);
   * `cppcoreguidelines-avoid-reference-coroutine-parameters` (i);
   * `cppcoreguidelines-c-copy-assignment-signature` (i);
   * `cppcoreguidelines-explicit-virtual-functions` (A, E);
   * `cppcoreguidelines-macro-usage` (i);
   * `cppcoreguidelines-misleading-capture-default-by-value` (i);
   * `cppcoreguidelines-missing-std-forward` (i);
   * `cppcoreguidelines-no-malloc` (i, v);
   * `cppcoreguidelines-pro-type-cstyle-cast` (v);
   * `cppcoreguidelines-rvalue-reference-param-not-moved` (i);
   * `cppcoreguidelines-special-member-functions` (i);
   * `cppcoreguidelines-virtual-class-destructor` (i);
   * `misc-confusable-identifiers` (i);
   * `misc-const-correctness` (i);
   * `misc-definitions-in-headers` (i);
   * `misc-misleading-bidirectional` (i);
   * `misc-misleading-identifier` (i);
   * `misc-redundant-expression` (i);
   * `misc-static-assert` (i);
   * `misc-unused-alias-decls` (i);
   * `misc-unused-parameters` (i);
   * `misc-unused-using-decls` (i);
   * `modernize-macro-to-enum` (i, v);
   * `modernize-type-traits` (v);
   * `modernize-use-using` (v);
   * `performance-no-int-to-ptr` (i);
   * `portability-*` (i);
   * `readability-avoid-unconditional-preprocessor-if` (i);
   * `readability-container-contains` (i);
   * `readability-container-data-pointer` (i);
   * `readability-duplicate-include` (i);
   * `readability-implicit-bool-conversion` (i);
   * `readability-isolate-declaration` (i);
   * `readability-operators-representation` (i);
   * `readability-qualified-auto` (i);
   * `readability-redundant-access-specifiers` (i);
   * `readability-redundant-member-init` (i);
   * `readability-redundant-preprocessor` (i);
   * `readability-simplify-boolean-expr` (i);
   * `readability-suspicious-call-argument` (i);
   * `readability-uppercase-literal-suffix` (i).

Following the steps described above, we get the following Clang-Tidy configuration:

```
-*
boost-*
bugprone-*
-bugprone-assignment-in-if-condition
-bugprone-easily-swappable-parameters
clang-analyzer-*
concurrency-*
cppcoreguidelines-*
-cppcoreguidelines-avoid-c-arrays
-cppcoreguidelines-avoid-do-while
-cppcoreguidelines-avoid-goto
-cppcoreguidelines-avoid-magic-numbers
-cppcoreguidelines-avoid-non-const-global-variables
-cppcoreguidelines-init-variables
-cppcoreguidelines-non-private-member-variables-in-classes
-cppcoreguidelines-owning-memory
-cppcoreguidelines-prefer-member-initializer
-cppcoreguidelines-pro-bounds-array-to-pointer-decay
-cppcoreguidelines-pro-bounds-constant-array-index
-cppcoreguidelines-pro-bounds-pointer-arithmetic
-cppcoreguidelines-pro-type-const-cast
-cppcoreguidelines-pro-type-reinterpret-cast
-cppcoreguidelines-pro-type-union-access
-cppcoreguidelines-pro-type-vararg
-cppcoreguidelines-use-default-member-init
misc-*
-misc-non-private-member-variables-in-classes
-misc-use-anonymous-namespace
modernize-*
-modernize-avoid-c-arrays
-modernize-use-default-member-init
-modernize-use-trailing-return-type
performance-*
-performance-avoid-endl
portability-*
readability-*
-readability-braces-around-statements
-readability-else-after-return
-readability-function-cognitive-complexity
-readability-function-size
-readability-identifier-length
-readability-identifier-naming
-readability-magic-numbers
-readability-named-parameter
```
