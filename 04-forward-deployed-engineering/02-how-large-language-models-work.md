# How Large Language Models Work: From Tokens to Next-Token Prediction

## Overview

A useful way to understand a Large Language Model (LLM) is to start with a simple idea:

> Given the existing sequence of tokens, predict what token should come next.

For example:

```text
I drink coffee every
```

The model may assign different probabilities to possible next tokens such as:

```text
morning
day
evening
```

The model then selects a token, appends it to the sequence, and performs the process again.

So generation is iterative rather than producing the entire response in a single step.

---

# 1. LLMs Predict the Next Token

Consider:

```text
The capital of India is
```

Conceptually, the model may produce a probability distribution such as:

```text
Delhi       94%
Punjab       2%
Bangalore    1%
Hyderabad    1%
Others       2%
```

The numbers are illustrative.

If `Delhi` is selected, the sequence becomes:

```text
The capital of India is Delhi
```

The model then predicts the next token using the new context.

This gives us the basic generation loop:

```text
Existing Context
       ↓
Predict Next Token
       ↓
Select Token
       ↓
Append Token
       ↓
Updated Context
       ↓
Predict Next Token
       ↓
Repeat
```

An important detail:

> An LLM predicts the next **token**, not necessarily the next word.

---

# 2. What Is a Token?

A common beginner assumption is:

```text
1 word = 1 token
```

That is not necessarily true.

A token can represent:

* A complete word
* Part of a word
* Punctuation
* A symbol
* A space-related unit
* Another frequently occurring text fragment

For example, conceptually:

```text
unbelievable
```

could be represented as:

```text
un + believ + able
```

The actual tokenization depends on the tokenizer being used.

Therefore:

```text
Token ≠ Word
```

A useful rough estimate sometimes used for English text is:

```text
1 token ≈ 4 characters
```

But this is only an approximation, not the definition of a token.

---

# 3. Tokens Become Token IDs

Computers ultimately perform mathematical operations.

Therefore, text needs to be converted into numerical representations.

A simplified vocabulary could look like:

```text
I           → 51
love        → 872
programming → 4381
coffee      → 992
```

So:

```text
"I love programming"
        ↓
["I", "love", "programming"]
        ↓
[51, 872, 4381]
```

There are three different concepts here:

```text
Original Text
     ↓
Tokens
     ↓
Token IDs
```

An important distinction:

> A token ID identifies a token. It does not represent the meaning of that token.

For example, if:

```text
cat → 1
car → 4
```

the fact that `4 > 1` does not mean a car is somehow greater than a cat.

The numbers are identifiers.

---

# 4. Why Embeddings Are Needed

Token IDs alone are not useful enough for neural-network processing.

The model needs numerical representations that can capture useful learned patterns.

A token can therefore be represented by a vector such as:

```text
king
 ↓
[0.41, -0.82, 1.34, 0.15, ...]
```

This vector is called an **embedding**.

The important distinction is:

```text
Token ID
    ↓
identifies the token

Embedding
    ↓
provides a learned numerical representation
```

The overall pipeline becomes:

```text
Text
 ↓
Tokens
 ↓
Token IDs
 ↓
Embeddings
 ↓
Neural Network Processing
```

The dimensions of real embeddings do not correspond to simple human-readable concepts such as "royalty" or "food." The model learns useful mathematical features during training.

---

# 5. Why Position Matters

Consider:

```text
Dog bites man.
```

and:

```text
Man bites dog.
```

The same major words are present, but the meaning is different.

Therefore, the model needs information about where tokens occur in the sequence.

Conceptually:

```text
Token Representation
        +
Position Information
        ↓
Position-aware Representation
```

So the model needs to consider both:

```text
What token is this?
        +
Where does it appear?
```

---

# 6. Why Context Matters

Consider:

```text
I deposited money at the bank.
```

Here, `bank` refers to a financial institution.

Now consider:

```text
We sat on the bank of the river.
```

Here, `bank` has a different meaning.

The surrounding context changes how the token should be interpreted.

Therefore:

> Tokens should not be interpreted independently.

The model needs representations that incorporate information from other tokens in the sequence.

This leads to the importance of **attention**.

---

# 7. What Attention Does

Consider:

```text
Rohit gave Aditya his laptop because his computer was broken.
```

To understand `his`, the model needs to examine other parts of the sentence.

Attention provides a mechanism for determining:

> Which other tokens are relevant to the current token, and how much should they influence its representation?

Another example:

```text
The animal didn't cross the street because it was too tired.
```

Here, `animal` may be particularly relevant to `it`.

If we change the sentence:

```text
The animal didn't cross the street because it was too wide.
```

The relevant relationship changes.

This demonstrates an important property:

> Attention is dynamic and depends on the current sequence.

---

# 8. Query, Key and Value

Attention uses three learned representations:

```text
Query
Key
Value
```

A useful conceptual analogy is search.

If we search for:

```text
best Java course
```

we can think of:

```text
Query
↓
What information am I looking for?

Key
↓
What kind of information does this item contain?

Value
↓
What information can this item contribute?
```

In an actual Transformer, Query, Key and Value are mathematical vectors rather than literal English questions.

Every token produces its own:

```text
Query
Key
Value
```

These allow the attention mechanism to determine which information from other tokens should influence the token currently being processed.

---

# 9. Self-Attention

Suppose we are processing:

```text
it
```

The model compares the Query associated with `it` against the Keys of other tokens.

Conceptually:

```text
Query("it")
      ↓
Key("animal")  → High relevance
Key("street")  → Medium relevance
Key("because") → Low relevance
```

The relevance scores are converted into weights.

The corresponding Values are then combined to produce an updated representation.

Conceptually:

```text
New Representation of "it"
        ≈
0.60 × Value(animal)
+
0.20 × Value(street)
+
0.05 × Value(cross)
+
...
```

The numbers above are illustrative.

This mechanism is called **self-attention** because tokens in the same sequence attend to other tokens in that sequence.

---

# 10. What Is a Transformer?

A common misconception is:

```text
Transformer = Attention
```

That is not correct.

Attention is an important component inside a Transformer.

A Transformer contains repeated processing blocks:

```text
Input Representations
        ↓
Transformer Block 1
        ↓
Transformer Block 2
        ↓
Transformer Block 3
        ↓
       ...
        ↓
Transformer Block N
        ↓
Final Representations
```

Each layer progressively transforms the representations.

Conceptually:

```text
Layer 1
Basic relationships
       ↓
Layer 2
Richer combinations
       ↓
Layer 3
More contextual relationships
       ↓
...
       ↓
Final contextual representation
```

By later layers, the representation contains richer information about the token, its position, surrounding context, and relationships with other tokens.

---

# 11. From Transformer Output to Next-Token Prediction

Consider:

```text
The capital of India is
```

After the sequence passes through the Transformer layers, the model produces a final contextual representation.

The model then needs to determine:

> Which token should come next?

Suppose the vocabulary contains many possible tokens.

The model produces a numerical score for each candidate.

These raw scores are called **logits**.

Conceptually:

```text
Delhi    → 12.8
Mumbai   →  8.1
Kolkata  →  5.2
Punjab   →  3.4
banana   → -4.1
...
```

The model has not produced probabilities yet.

These are scores.

---

# 12. Softmax Converts Logits Into Probabilities

The logits are passed through a mathematical function called **Softmax**.

Conceptually:

```text
Logits
   ↓
Softmax
   ↓
Probability Distribution
```

For example:

```text
Delhi      92%
Kolkata     2%
Punjab      2%
Mumbai      1%
Chennai     1%
Others      2%
```

The probabilities form a distribution over possible next tokens.

Therefore:

```text
Context
  ↓
Logits
  ↓
Softmax
  ↓
Next-token probabilities
```

---

# 13. Autoregressive Generation

The model does not normally generate the complete answer in one operation.

Instead, it generates incrementally.

```text
Prompt
  ↓
Predict Token 1
  ↓
Append Token 1
  ↓
Predict Token 2
  ↓
Append Token 2
  ↓
Predict Token 3
  ↓
...
```

This is called **autoregressive generation**.

The model uses previously generated output as part of the context for future predictions.

For example:

```text
The capital of India is
```

↓

```text
The capital of India is Delhi
```

↓

```text
The capital of India is Delhi.
```

↓

The process continues.

This repeated next-token prediction is the basis of autoregressive text generation.

---

# 14. Model vs Decoding Strategy

This distinction is important.

The model produces:

```text
Next-Token Probability Distribution
```

A decoding strategy decides:

```text
Which token should actually be selected?
```

So:

```text
MODEL
  ↓
Next-Token Probability Distribution
  ↓
DECODING STRATEGY
  ↓
Selected Token
```

The lecture introduces strategies such as:

* Greedy decoding
* Sampling
* Temperature

---

# 15. Greedy Decoding

Greedy decoding chooses the token with the highest probability.

For example:

```text
Java       40%  ← selected
Python     35%
C++        10%
JavaScript  8%
Rust        4%
```

The highest-probability token is selected at each step.

The limitation is that always choosing the highest-probability token can make output:

* Predictable
* Repetitive
* Less varied

---

# 16. Sampling

Instead of always choosing the highest-probability token, sampling treats the probabilities as a weighted selection process.

For example:

```text
Java       40%
Python     35%
C++        15%
Rust       10%
```

Java is the most likely selection, but it is not guaranteed to be selected.

A lower-probability token may sometimes be chosen.

Therefore:

> Higher probability means more likely, not guaranteed.

This helps explain why the same prompt can sometimes produce different responses.

---

# 17. Temperature

Temperature changes how sharp or flat the probability distribution becomes before sampling.

Conceptually:

```text
Adjusted Logit = Original Logit / Temperature
```

### Temperature = 1

The logits remain unchanged.

```text
5, 4, 3, 2, 1
```

### Lower Temperature

For example:

```text
Temperature = 0.5

5, 4, 3, 2, 1
        ↓
10, 8, 6, 4, 2
```

The differences between logits become larger.

After Softmax, stronger candidates become more dominant.

Lower temperature generally produces:

* More predictable output
* More consistent output
* Less variation

### Higher Temperature

For example:

```text
Temperature = 2

5, 4, 3, 2, 1
        ↓
2.5, 2, 1.5, 1, 0.5
```

The logits move closer together.

After Softmax, lower-ranked tokens receive relatively more probability.

Higher temperature generally increases:

* Variation
* Unexpected wording
* Diversity

But it can also increase the chance of:

* Mistakes
* Irrelevant continuations
* Nonsensical output

---

# 18. Complete LLM Generation Pipeline

Putting the concepts together:

```text
User Text
   ↓
Tokenizer
   ↓
Tokens
   ↓
Token IDs
   ↓
Embeddings
   ↓
Position Info
```
