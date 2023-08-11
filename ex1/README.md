# Exercise 1

In this exercise, you will model the following protocol, an authenticated Diffie-Hellman key exchange.
In the following, `{m}s` denotes the symmetric encryption of message `m` with key `s`.

```mermaid
sequenceDiagram
  Note over Alice: Sample skA
  Note over Bob: Sample skB
  Alice->>Bob: request, Alice, Bob, g^skA
  Note over Bob: Verify g^skA
  Note over Bob: Compute s := (g^skA)^skB
  Bob->>Alice: {ack}s, Alice, Bob, g^skB
  Note over Alice: Verify g^skB
  Note over Alice: Compute s := (g^skB)^skA
  Alice->>Bob: {m}s
```

The file `exADH.spthy` provides you with a skeleton file.
As it stands, the file is in correct Tamarin syntax.
We recommend that you regular try to load your model to Tamarin to check your syntax.

The model uses two built-in *message theories*.
Message theories are sets of function and equations definitions for common use-cases.
For this exercise, the following functions are important: `'g'^sk` (Diffie-Hellman exponentiation; it is a convention to use the constant `'g'` as base of the exponentiation), `senc(m, k)` (symmetric encryption of message `m` under key `k`), and `sdec(m, k)` (symmetric decryption of message `m` under key `k`).

We do not suggest to follow this link now, but you can find the documentation for the built-in message theories [here](https://tamarin-prover.github.io/manual/master/book/004_cryptographic-messages.html#sec:builtin-theories).

The model also comes with an *executability lemma*.
Executability lemmas serve as sanity checks that your model works as you think it does.
This executability lemma ensures that every message can be sent (all parts of your model are reachable).
We recommend verifying the executability lemma whenever load your model after you made some changes.
This catches modelling errors early and thus saves you pain!

Now to the exercise...

## Step 1

The protocol above assumes that Alice and Bob have access to authentic public keys.
Start by implementing the rule `Ltk`, which should model a participant generating a fresh public-private key pair.

You can use the hints below if you get stuck, but we encourage to first try solving the entire exercise before using hints.
The hints often propose *one* way to solve the exercise, but in general, there is no right or wrong way to model a protocol!
You can also talk to your peers, which is usually much more insightful than using hints!

<details>
  <summary>How do I model key generation?</summary>
  Use the Fr fact to model the generation of a fresh, unguessable value.
</details>

<details>
  <summary>How do I identify participants?</summary>
  It is a common convention among modellers to use public values (starting with a dollar) to model identifiers for participants.
</details>

<details>
  <summary>How do I model that a participants memorizes their key?</summary>
  Use persistent facts (starting with a !).
</details>

## Step 2

Now model all three protocol steps one-by-one.
We suggest following the state-lookup/message-receive + state-write/message-send pattern.
The skeleton file has been prepared to follow this pattern.
For each rule's premise and conclusion, you will need to add more facts, though!

Note that Tamarin supports some laws for `^`, in particular, `(a^b)^c = (a^c)^b`.

Don't forget to regularly load your model, and check the executability lemma!

Again, if you get stuck, you can use the hints below or talk to your peers.

<details>
  <summary>What is the state-lookup/message-receive + state-write/message-send pattern?</summary>
  In this pattern, one models what are usually the arrows in a message sequence chart with one rule each.
  Each of these rules is modelled as follows.
  In the rule's premise, look up the participants state and receive a message.
  In the conclusion of a rule, update the state (using a new fact!) and send a message.
</details>

<details>
  <summary>How can participants verify a public key?</summary>
  Earlier, you modelled the key generation.
  Nothing prevents you from looking up someone else's key!
  Then, you can use the Eq fact or pattern matching to check for equality between a key received and a key looked-up.
</details>

## Step 3

Now you can prove properties of your model!
Look at the lemmas that are commented out in the skeleton file.
Do you expect them to hold for the protocol specification above?

Comment them out and try proving them!
Discuss your results with your peers.

<details>
  <summary>Expected results</summary>
  All lemmas should be true.
</details>
