# Sexy Clang-Tidy

This project contains a Clang-Tidy configuration which I deem reasonable for my projects.

## Configuration rationale

1. We start out with CLion’s default Clang-Tidy configuration. For reference, these are the checks which are enabled by
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
   bugprone-suspicious-memory-comparison
   bugprone-suspicious-memset-usage
   bugprone-suspicious-missing-comma
   bugprone-suspicious-realloc-usage
   bugprone-suspicious-semicolon
   bugprone-suspicious-string-compare
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

2. We disable vendor- and application-specific checks: I don’t think that a default linting configuration should depend
   on any particular style.

   The following listing describes the checks to be disabled:

   ```
   cert-*
   google-*
   hicpp-*
   mpi-*
   openmp-*
   ```

   Clang-Tidy has a huge amount of checks; it is unrealistic to go through them all. We’ll assume that all other checks
   enabled by CLion’s default configuration are adequate.

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

   ```
   bugprone-bool-pointer-implicit-conversion
   bugprone-branch-clone
   bugprone-compare-pointer-to-member-virtual-function
   bugprone-exception-escape
   bugprone-implicit-widening-of-multiplication-result
   bugprone-inc-dec-in-conditions
   bugprone-incorrect-enable-if
   bugprone-infinite-loop
   bugprone-multi-level-implicit-pointer-conversion
   bugprone-multiple-new-in-one-expression
   bugprone-narrowing-conversions
   bugprone-non-zero-enum-to-bool-conversion
   bugprone-not-null-terminated-result
   bugprone-optional-value-conversion
   bugprone-redundant-branch-condition
   bugprone-shared-ptr-array-mismatch
   bugprone-signal-handler
   bugprone-signed-char-misuse
   bugprone-standalone-empty
   bugprone-stringview-nullptr
   bugprone-unchecked-optional-access
   bugprone-unhandled-exception-at-new
   bugprone-unique-ptr-array-mismatch
   bugprone-unsafe-functions
   cppcoreguidelines-avoid-capturing-lambda-coroutines
   cppcoreguidelines-avoid-const-or-ref-data-members
   cppcoreguidelines-avoid-reference-coroutine-parameters
   cppcoreguidelines-c-copy-assignment-signature
   cppcoreguidelines-explicit-virtual-functions
   cppcoreguidelines-macro-to-enum
   cppcoreguidelines-macro-usage
   cppcoreguidelines-misleading-capture-default-by-value
   cppcoreguidelines-missing-std-forward
   cppcoreguidelines-no-malloc
   cppcoreguidelines-no-suspend-with-lock
   cppcoreguidelines-noexcept-destructor
   cppcoreguidelines-noexcept-move-operations
   cppcoreguidelines-noexcept-swap
   cppcoreguidelines-pro-type-cstyle-cast
   cppcoreguidelines-rvalue-reference-param-not-moved
   cppcoreguidelines-special-member-functions
   cppcoreguidelines-virtual-class-destructor
   misc-confusable-identifiers
   misc-const-correctness
   misc-definitions-in-headers
   misc-header-include-cycle
   misc-include-cleaner
   misc-misleading-bidirectional
   misc-misleading-identifier
   misc-redundant-expression
   misc-static-assert
   misc-unused-alias-decls
   misc-unused-parameters
   misc-unused-using-decls
   modernize-macro-to-enum
   modernize-type-traits
   modernize-use-constraints
   modernize-use-std-print
   modernize-use-using
   performance-enum-size
   performance-no-int-to-ptr
   performance-noexcept-destructor
   performance-noexcept-swap
   readability-avoid-unconditional-preprocessor-if
   readability-container-contains
   readability-container-data-pointer
   readability-duplicate-include
   readability-implicit-bool-conversion
   readability-isolate-declaration
   readability-operators-representation
   readability-qualified-auto
   readability-redundant-access-specifiers
   readability-redundant-member-init
   readability-redundant-preprocessor
   readability-reference-to-constructed-temporary
   readability-simplify-boolean-expr
   readability-suspicious-call-argument
   readability-uppercase-literal-suffix
   ```

Following the steps described above, we get the following Clang-Tidy configuration:

```
-*
boost-*
bugprone-*
-bugprone-assignment-in-if-condition
-bugprone-easily-swappable-parameters
-bugprone-empty-catch
-bugprone-switch-missing-default-case
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
