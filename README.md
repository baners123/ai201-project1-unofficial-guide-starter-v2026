

# The Unofficial Guide

## What This Does

The Unofficial Guide is a question answering system built around the `campus_life` corpus. It loads documents, splits them into chunks, creates embeddings, and stores them in a vector database. When a user asks a question, the system retrieves matching chunks and generates an answer from those documents. A relevance cutoff stops questions that the corpus does not cover.

## Chunking Strategy

I used a chunk size of 500 characters with 75 characters of overlap. The `campus_life` documents are short student posts, so I wanted each chunk to focus on one idea while keeping enough context to understand it.

The starter used fixed 800-character chunks. Many documents were shorter than 800 characters, so the starter often kept the full document as one chunk. My chunker uses paragraph boundaries when possible and overlap when longer text needs to be split.

## Sample Chunks

Replace these placeholders with five chunks produced by the program. Include the source filename and function name for each chunk.

### Chunk 1
Source: `REPLACE_WITH_SOURCE_1.txt`
Function: `chunk_text`

> REPLACE WITH ACTUAL CHUNK 1

### Chunk 2
Source: `REPLACE_WITH_SOURCE_2.txt`
Function: `chunk_text`

> REPLACE WITH ACTUAL CHUNK 2

### Chunk 3
Source: `REPLACE_WITH_SOURCE_3.txt`
Function: `chunk_text`

> REPLACE WITH ACTUAL CHUNK 3

### Chunk 4
Source: `REPLACE_WITH_SOURCE_4.txt`
Function: `chunk_text`

> REPLACE WITH ACTUAL CHUNK 4

### Chunk 5
Source: `REPLACE_WITH_SOURCE_5.txt`
Function: `chunk_text`

> REPLACE WITH ACTUAL CHUNK 5

## Sample Answer

Question:

> REPLACE WITH ONE TEST QUESTION

Answer:

> REPLACE WITH THE GENERATED ANSWER

Source:

`REPLACE_WITH_SOURCE_FILE.txt`

### Relevance Cutoff

I used a relevance cutoff of `REPLACE_WITH_YOUR_CUTOFF`.

For the five questions covered by my corpus, the best distances were:

| Question | Best Distance |
| --- | ---: |
| 1 | REPLACE |
| 2 | REPLACE |
| 3 | REPLACE |
| 4 | REPLACE |
| 5 | REPLACE |

For five questions outside my corpus, the best distances were:

| Question | Best Distance |
| --- | ---: |
| 1 | REPLACE |
| 2 | REPLACE |
| 3 | REPLACE |
| 4 | REPLACE |
| 5 | REPLACE |

I placed the cutoff between the highest distance from a covered question and the lowest distance from an outside question.

## How I Used AI

I used ChatGPT to break the assignment into steps and review my chunking plan. It suggested a 500-character chunk size with 75 characters of overlap. I used that as a starting point and planned to compare it with the chunks produced by my corpus.

I also used ChatGPT to review the relevance gate. It explained that the program should retrieve results, check the best distance, and stop before generation when the result is above the cutoff. I adapted that logic to the starter code.

