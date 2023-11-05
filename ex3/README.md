# OAuth 2.0 Exercise

In this exercise, you will model a variant of [RFC6749](https://www.rfc-editor.org/rfc/rfc6749), better known as OAuth 2.0.
The file `OAuth.md` provides you with a simplified version of the specification.

The goal of this exercise is to learn how you can Tamarin apply to actual problems, and this is often harder than you think.
The file `exOAuth.spthy` provides you with a small template for this exercise and contains a model of a secure channel.

As with every modelling exercise, remember that there is no clear right or wrong.

## Step 1: The Spec

Familiarize yourself with the OAuth 2.0 specification.
In this exercise, we will model the "Authorization Code Grant" for "confidential clients" (using the terminology of the original specification).
In the excerpt of the specification, we dropped the distinction of confidential and other clients and simply speak of "clients."

We suggest that you first try to get a general understanding of the entire specification before you dwell on details too much.

Make sure that you have a good understanding of:

- What is the client, the authorization endpoint, the redirection endpoint, and the token endpoint?
- Which messages are sent by the client to the authorization endpoint (and back)?
- Which messages are sent by the client to the token endpoint (and back)?
- Which channels are used between what clients?

## Step 2: The Model

Create a first model of the authorization code flow in Tamarin.
The point of this model is to develop a model that you can express properties about and that you can refine later.

We **suggest** making the following abstractions:

- Use a secure channel for messages sent from and to clients.
You will refine your model later in step 4!
- Model the authorization server perfectly authentication users.
- Do not model how access tokens are used, only how they are obtained.

Make sure to prove an executability lemma that establishes that your model can be executed.

## Step 3: The Properties

Think about what security properties you would expect OAuth 2.0 to provide.
In our solution, we implemented two lemmas.

If you need some inspiration, you can read the Section [Security Considerations](https://www.rfc-editor.org/rfc/rfc6749#section-10) of the original RFC.

## Step 4: Refining Everything

In step 2, you made quite substantial abstractions regarding the channels used.
Probably, you could immediately prove the security properties in step 3.
At least, this was the case when we solved the exercise.

We want to encourage you now to refine the message passing.
In step 2, we encouraged you to abstract the channels as secure.
Now, think about the following:

- The secure channel model did not allow for adversary activity at all.
It might be desirable that the adversary can send or receive on the secure channel when they compromised some endpoints.
Implement rules that allow the adversary to do so, and rules that allow for the compromise of endpoints.
Remember to strengthen your executability lemma s.t. it forbids any form of compromise (the goal of the lemma is to establish that the protocol can be executed *without* adversary involvement).
- Which channels are used between the client, authorization endpoint, redirection endpoint, and token endpoint in the specification?
Are they secure?
Are they only confidential (everyone can send, but not only the intended recipient receive)?
Are they only authentic (everyone can read, but only the intended sender send)?
None of that?
- If you decide for an insecure channel, you can simply use `In`/`Out` facts.
In any other case, implement that channel model analogously to the secure channel, and use this channel for some of the message passing.
- After all the above, your security lemmas will probably not hold anymore.
Think about a threat model under which they can hold again, e.g., by ruling out the compromise of certain endpoints.

## Further Reading

If you are interested in more details, we suggest reading the following paper, in which the authors formally analyzed the OAuth 2.0 specification in the symbolic model.

> Daniel Fett, Ralf Küsters, and Guido Schmitz. 2016. A Comprehensive Formal Security Analysis of OAuth 2.0. In Proceedings of the 2016 ACM SIGSAC Conference on Computer and Communications Security (CCS '16). Association for Computing Machinery, New York, NY, USA, 1204–1215. https://doi.org/10.1145/2976749.2978385
