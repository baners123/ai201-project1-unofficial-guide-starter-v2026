# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---
1. For at least 4 of my 5 test questions, the retrieved chunks include one that contains the answer.

Reason: I chose 4 out of 5 because retrieval should find the needed information for most of my test questions while allowing one miss.

2. Every answer the system produces names at least one source document.

Reason: I included this so I can check where each answer came from.

3. When I ask a question my documents clearly do not cover, the relevance gate stops it and the system returns "I don't have enough information about that" in at least 4 of 5 tries.

Reason: I chose 4 out of 5 because the system should reject most questions that the corpus does not cover while allowing one retrieval error.

4. At least 4 of 5 sample chunks contain a complete thought that can be understood without reading the chunk before or after it.

Reason: I chose 4 out of 5 because most chunks should keep enough context to make sense on their own, even if one split needs adjustment.

5. For at least 4 of my 5 test questions, the final answer contains the expected word or phrase listed in `questions.py`.

Reason: I chose 4 out of 5 because the answers should include the information I expected for most test questions while allowing one missed result.
