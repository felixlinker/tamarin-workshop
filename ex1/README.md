# Symmetric Encryption Exercise

In this exercise, you will familiarize yourself with the adversary in Tamarin.
The file `exSenc.spthy` provides you with a skeleton model.
As it stands, the model is in correct Tamarin syntax.
We recommend that you regularly try to load your model to Tamarin to check your syntax.

The model has four parts:

- In line 4, we import the `symmetric-encryption` message theory.
This theory defines the functions `senc/2` and `sdec/2`, with the equation `sdec(senc(m, k), k) = m`.
- Lines 6-9 define a rule in which participant `$A` generates a fresh secret key `~k`.
- Lines 11-15 provide a skeleton rule to send a message (we will look at this later).
- Lines 17-18 defines a *secrecy* lemma.
The lemma expresses:

> Whenever participant `p` sends message `m`, there exists no point in time at which the adversary can learn `m`.

`K(m)` is the fact that expresses that the adversary knows `m`.

## Step 1

Fill the rule `SendMsg` with life!
At the end, participant `$A` should send message `~m` symmetrically encrypted under their previously generated key.

When you do so, uncomment the `Send` fact in the rule's label.
Note that this means the variables `$A` and `~msg` must come from somewhere.

Once you have implemented the rule, load the interactive mode of Tamarin and try verifying the `Secrecy` lemma.
Does it hold?
What would you expect?

## Step 2

It is a common modelling pattern in Tamarin to include a rule that leaks a participant's key.
Usually, this is done to model more granular notions of compromise.
Sometimes, certain security properties hold if only some types of keys are not leaked but others may leak.

Model a new rule `Leak` that leaks a previously generated key to the adversary.

If you have done so, verify the `Secrecy` lemma again.
Given that the participants key leaked, the adversary should now be able to learn the previously sent message.

Tamarin gives you a graphical output when it finds an attack.
Try relating this output to you model.
Can you make sense of it?

## Step 3

To "document" which key leakage is permitted, Tamarin modelers often use a `Compromised(p)` fact in formulas to document which participant's keys leaked.

Add such a fact to one of the three rules of the model.
Then, write a new `SecrecyWeakened` expressing:

> Whenever participant `p` sends message `m`, there exists no point in time at which the adversary can learn `m` or there exists a point in time at which `p`'s key leaked.

Try verifying this lemma.
Which result would you expect?
