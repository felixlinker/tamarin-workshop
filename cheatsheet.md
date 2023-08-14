# Tamarin Cheat Sheet

## Rules

Complete reference: [link](https://tamarin-prover.github.io/manual/master/book/005_protocol-specification-rules.html)

```tamarin
rule RuleName:
  let t3 = t2 in  // optional
  [ F1(t1, t2) ]  // premise (multiset/list of facts)
  --[ L(t1) ]->
  [ F2(t3) ]      // conclusion (multiset/list of facts)
```

- `F1`, `F2` are *facts*
- `L` is an *action fact*
- `t1`, `t2`, `t3` are *terms*

## Reserved Facts

- `Out(m)` - Send `m` (only in conclusion)
- `In(m)` - Receive `m` (only in premise)
- `Fr(x)` - Generate unguessable/fresh value `x` (only in premise)

## Terms

Complete reference: [link](https://tamarin-prover.github.io/manual/master/book/004_cryptographic-messages.html)

- Messages (anything), e.g., `m`
- Constants, e.g., `'constant'`
- Unguessable (fresh) values, e.g., `~k`
- Public values, e.g, `$P`
- Function application `f(t1, t2)`

## Functions + Equations

### Custom

Define function `senc` with 2 arguments and `sdec` with 2 arguments:

```tamarin
functions: senc/1, sdec/2
```

Define equation for aforementioned functions:

```tamarin
equations: sdec(senc(m, k), k) = m
```

### Built-Ins

Import [built-in theory](https://tamarin-prover.github.io/manual/master/book/004_cryptographic-messages.html#sec:builtin-theories) `symmetric-encryption` (which "imports" custom definition from above):

```tamarin
builtins: symmetric-encryption
```

## Lemma + Restrictions

Complete reference: [link](https://tamarin-prover.github.io/manual/master/book/007_property-specification.html)

### Formulas

- `All v. p`: For all `v`, `p` holds
- `Ex v. p`: For some `v`, `p` holds
- `L(t1) @ #x`: Action fact `L` occurs at timepoint `#x`
- `#i < #j`: Timepoint `#i` is smaller than timepoint `#j`
- `t1 = t2` / `#i = #j`: Terms (or timepoints) are equal
- Standard logical operands: `p & q`, `p | q`, `not p`,`p ==> q` (equivalent to `p | not q`)

### Lemmas

Properties you want to be true of your model.

```tamarin
// Usually: verify that something you expect to be impossible is impossible
lemma ForAll:
  all-traces
  "All m #x. Secret(m) @ #x ==> not Ex #y. K(m) @ #y"

// Usually: verify that something you expect to be possible is possible
lemma ForSome:
  exists-trace
  "Ex #t. ProtocolCompleted() @ #t"
```

### Restrictions

Assumptions of your model.
Often used to implement "helpers".

```tamarin
restriction Eq:
  "All t1 t2 #x. Eq(t1, t2) @ #x ==> t1 = t2"
```

## Abstract Syntax Definition

- Variables must start with a letter or _
- Capitalized variables must start with a capital letter or _
- Generally, Tamarin is case-sensitive and ignores repeated line breaks, spaces, or tabs
- Note: This syntax definition is not complete!

```tamarin
<MODEL> ::=
  theory <VAR>
  begin
  { <IMPORT> / <F-DEF> / <EQ-DEF> / <RULE> / <LEMMA> / <RESTRICTION> }*
  end

<IMPORT> ::=
  builtins: LIST{ <THEORY> }+

<F-DEF> ::=
  functions: LIST{ <VAR>/<INT> }+

<EQ-DEF> ::=
  equations: LIST{ <TERM> = <TERM> }+

<RULE> ::=
  rule <VAR>:
    { let LIST{ <VAR> = <TERM> }+ in }?
    [ LIST{ <FACT> }* ] --{[ LIST{ <ACTION-FACT> }* ]-}?> [ LIST{ <FACT> }* ]

<LEMMA> ::=
  lemma <VAR>{[ LIST{ <ANNOTATION> }* ]}?:
    { exists-trace / all-traces }?
    "<FORMULA>"

<RESTRICTION> ::=
  restriction <VAR>:
    "<FORMULA>"

<FORMULA> ::=
    All LIST{ {#}?<VAR> }+. <FORMULA>
  / Ex LIST{ {#}?<VAR> }+. <FORMULA>
  / <FORMULA> & <FORMULA>
  / <FORMULA> | <FORMULA>
  / not <FORMULA>
  / <FORMULA> ==> <FORMULA>
  / <FACT> @ {#}?<VAR>
  / {#}?<VAR> < {#}?<VAR>
  / <TERM> = <TERM>

<TERM> ::=
    { ~ / $ }?<VAR>
  / '<STRING>'
  / <VAR>( LIST{ <TERM> }* )

<ACTION-FACT> ::=
  {!}?<CAPITALIZED-VAR>( LIST{ <TERM> }* )

<RESERVED-FACT> ::=
  Out(<TERM>) / In(<TERM>) / Fr({~}?<VAR>)

<FACT> ::=
  <ACTION-FACT> / <RESERVED-FACT>
```

Conventions:

- `<P> ::= Q` defines pattern `P` to be `Q`
- `<P>` references pattern `P`
- `P / Q` means `P` or `Q`
- `{ P }?` means optionally `P` (`P` may be omitted)
- `{ P }*` means zero or more repetitions of `P`
- `{ P }+` means one or more repetitions of `P`
- `LIST{ P }*` means a list of zero or more repetitions of `P`, conjoined with `,`, e.g., `P, P`
- `LIST{ P }+` means a list of one or more repetitions of `P`, conjoined with `,`
