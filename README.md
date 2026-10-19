# The Unofficial Guide

## What This Does

The Unofficial Guide is a question answering system built around the `campus_life` corpus. It loads documents, divides them into chunks, creates embeddings, and stores them in a vector database. When a user asks a question, the system retrieves relevant chunks and generates an answer using those documents.

The system also uses a relevance cutoff. If the retrieved information is too far from the user's question, the system refuses to answer instead of generating an answer from unrelated information.

## Chunking Strategy

The `campus_life` corpus contains short student posts. After indexing, the system loaded 88 documents containing 27,908 characters and produced 88 chunks.

The chunks averaged 317 characters. The shortest chunk was 178 characters and the longest was 549 characters.

The current indexed corpus used `chunker.py::fallback_split`. Since most of the documents are already short and focused on one topic, many documents remain as a single chunk. This keeps information from one student post together instead of separating related details.

## Sample Chunks

### Chunk 1

Source: `admin_add_drop_deadline.txt#0`

Function: `chunker.py::fallback_split`

> On the add/drop deadline
>
> You can add a course through the end of the second week. Dropping is a longer window, through the end of week six, but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.

### Chunk 2

Source: `course_biol_160.txt#0`

Function: `chunker.py::fallback_split`

> BIOL 160 Cell Biology
>
> I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.
>
> Expect 9 to 11 hours a week, the heaviest first-year course by reputation.
>
> The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.

### Chunk 3

Source: `course_hist_118_workload.txt#0`

Function: `chunker.py::fallback_split`

> Workload for HIST 118 Modern World History
>
> People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.
>
> It's front-loaded, the first month is heavier than the rest, partly because you're learning the format.

### Chunk 4

Source: `dining_pellew_dining_hall_followup.txt#0`

Function: `chunker.py::fallback_split`

> Re: Pellew Dining Hall
>
> Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.
>
> Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.

### Chunk 5

Source: `housing_innisfree_hall.txt#0`

Function: `chunker.py::fallback_split`

> Innisfree Hall, what it's actually like
>
> Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.
>
> The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.
>
> The bad: no air conditioning, which matters for the first three weeks of September.
>
> Laundry costs $1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and the short wing is much quieter.

## Sample Answer

Question:

> Is the housing lottery random?

Answer:

> The housing lottery is not entirely random in the way most people assume. Rising sophomores get a number drawn at random, but juniors and seniors are ordered by accumulated credit hours first, with random tie-breaks used only after that.

Source:

`admin_housing_lottery.txt`

The system retrieved `admin_housing_lottery.txt` as its best source for this question.

## Relevance Cutoff

I used a relevance cutoff of `0.6`. Lower distances indicate that a retrieved chunk is more closely related to the question.

For the five questions covered by the corpus, the best distances were:

| Question | Best Distance |
| --- | ---: |
| Commons lunch wait times | 0.308 |
| Housing lottery | 0.225 |
| Dorm laundry | 0.524 |
| Registration and waitlists | 0.522 |
| Dorm quiet hours | 0.410 |

For the five questions outside the corpus, the best distances were:

| Question | Best Distance |
| --- | ---: |
| What is the capital of Mongolia? | 0.825 |
| How do I change the oil in a diesel engine? | 0.934 |
| Who won the 1994 World Cup? | 0.886 |
| What is the recommended dosage of ibuprofen for a headache? | 0.844 |
| How do I write a for loop in Rust? | 0.896 |

The highest distance among the five covered questions was `0.524`. The lowest distance among the five outside questions was `0.825`.

The `0.6` cutoff falls between these groups. All five covered questions passed the relevance gate, while all five outside questions were refused.

# Unit 2: Testing

## Run Log: Before

I ran the five evaluation questions three times each using:

`python run_eval.py --label before`

Caching was disabled during evaluation. The run was saved in:

`results/run_2026-10-18_2050_before.md`

The project did not contain `scorer.py`, so `run_eval.py` did not automatically score the generated answers. I used the retrieval distances, retrieved sources, relevance gate, and generated responses as evidence.

| Test | Target | Run 1 | Run 2 | Run 3 | Verdict |
| --- | --- | ---: | ---: | ---: | --- |
| Commons lunch wait times | Distance below 0.6 | 0.308 | 0.308 | 0.308 | MET |
| Housing lottery | Distance below 0.6 | 0.225 | 0.225 | 0.225 | MET |
| Dorm laundry | Distance below 0.6 | 0.524 | 0.524 | 0.524 | MET |
| Registration and waitlists | Distance below 0.6 | 0.522 | 0.522 | 0.522 | MET |
| Dorm quiet hours | Distance below 0.6 | 0.410 | 0.410 | 0.410 | MET |

The relevance gate also correctly refused all five out-of-scope questions.

| Out-of-Scope Question | Best Distance | Result |
| --- | ---: | --- |
| Capital of Mongolia | 0.825 | Refused |
| Diesel engine oil change | 0.934 | Refused |
| 1994 World Cup | 0.886 | Refused |
| Ibuprofen dosage | 0.844 | Refused |
| Rust for loop | 0.896 | Refused |

Gate result: `5/5` out-of-scope questions refused.

### Evidence

For the Commons lunch question, the best result was:

`dining_kestrel_commons_followup.txt`

Best distance: `0.308`

For the housing lottery question, the best result was:

`admin_housing_lottery.txt`

Best distance: `0.225`

For the laundry question, the best result was:

`housing_old_brewhouse_laundry.txt`

Best distance: `0.524`

Other highly ranked results also included laundry information from Aldridge Hall, Tamsin Court, and Innisfree Hall.

For the registration and waitlists question, the best result was:

`advising_registration.txt`

Best distance: `0.522`

The retrieved results also included `admin_add_drop_deadline.txt`.

For the quiet hours question, the best result was:

`housing_morrow_house_noise.txt`

Best distance: `0.410`

Other highly ranked results included noise information for Aldridge Hall, Innisfree Hall, and Old Brewhouse.

## Verdicts

### Criterion 1: Commons Lunch Wait Times

**Verdict: MET**

All three runs produced a best distance of `0.308`, which is below the `0.6` relevance cutoff. The highest ranked result was related to Kestrel Commons and contained dining information.

### Criterion 2: Housing Lottery

**Verdict: MET**

All three runs produced a best distance of `0.225`. This was the strongest retrieval result among the five evaluation questions. The highest ranked source was `admin_housing_lottery.txt`, which directly addresses the question.

### Criterion 3: Dorm Laundry

**Verdict: MET**

All three runs produced a best distance of `0.524`. This is below the `0.6` cutoff. The retrieved documents contained laundry information from several residence halls.

### Criterion 4: Registration and Waitlists

**Verdict: MET**

All three runs produced a best distance of `0.522`. The system retrieved `advising_registration.txt` first and also found `admin_add_drop_deadline.txt`.

### Criterion 5: Dorm Quiet Hours

**Verdict: MET**

All three runs produced a best distance of `0.410`. The top results came from housing documents discussing noise levels, which were relevant to the question.

The relevance gate also met its expected behavior. All five unrelated questions were above the `0.6` cutoff and were refused.

## Diagnoses

The five main evaluation questions passed the relevance gate in all three runs. Because of this, there was not a clear retrieval failure among these questions.

The weakest retrieval results were the laundry question at `0.524` and the registration question at `0.522`. Both were still below the `0.6` cutoff, but they were closer to the cutoff than the other questions.

### Laundry

Stage: Retrieval

The system found relevant laundry documents, but the semantic similarity was weaker than it was for questions such as the housing lottery. This may happen because laundry information appears across several housing documents instead of one central document.

The retrieved results still contained the information needed to answer the question.

### Registration and Waitlists

Stage: Retrieval

The first result was `advising_registration.txt`, which was relevant. However, some unrelated dining documents also appeared near the top of the retrieval results.

This suggests that semantic retrieval can return some distractor chunks when wording in unrelated documents happens to have similar embeddings.

### Relevance Gate

Stage: Gate

The gate performed consistently. The highest in-scope distance was `0.524`, while the lowest out-of-scope distance was `0.825`.

This leaves a gap of `0.301` between the two groups. The `0.6` cutoff falls inside that gap and correctly separated all ten evaluation questions.

## The Improvement

I investigated retrieval depth as the improvement area because the registration and laundry questions had the weakest in-scope distances.

I tested retrieval with a larger `top-k` value to inspect additional chunks. The goal was to determine whether relevant information existed slightly lower in the retrieval ranking and whether retrieval depth was contributing to weaker results.

The diagnostic command used was:

`python app.py retrieve "QUESTION" --top-k 7`

This showed that increasing retrieval depth can expose more candidate chunks, but it did not change the best semantic distance produced by the embedding search.

The recorded evaluation configuration itself remained unchanged. Because of that, I expected the after run to show little or no difference. I kept this result instead of changing the data because the purpose of the evaluation is to measure what actually happened.

### Run Log: After

I reran the complete evaluation using:

`python run_eval.py --label after`

The result was saved in:

`results/run_2026-10-18_2051_after.md`

| Test | Before | After Run 1 | After Run 2 | After Run 3 |
| --- | ---: | ---: | ---: | ---: |
| Commons lunch wait times | 0.308 | 0.308 | 0.308 | 0.308 |
| Housing lottery | 0.225 | 0.225 | 0.225 | 0.225 |
| Dorm laundry | 0.524 | 0.524 | 0.524 | 0.524 |
| Registration and waitlists | 0.522 | 0.522 | 0.522 | 0.522 |
| Dorm quiet hours | 0.410 | 0.410 | 0.410 | 0.410 |

The out-of-scope results also remained the same:

| Out-of-Scope Question | Before | After |
| --- | ---: | ---: |
| Capital of Mongolia | 0.825 | 0.825 |
| Diesel engine oil change | 0.934 | 0.934 |
| 1994 World Cup | 0.886 | 0.886 |
| Ibuprofen dosage | 0.844 | 0.844 |
| Rust for loop | 0.896 | 0.896 |

The gate refused `5/5` out-of-scope questions before and `5/5` after.

### Improvement Result

The measured results did not improve. The before and after retrieval distances were identical.

This showed that simply inspecting more retrieval results did not improve the best semantic match. A stronger next improvement would need to change how results are ranked, how documents are chunked, or how semantic retrieval is combined with keyword matching.

The unchanged result is still useful because it shows that retrieval depth was not the main cause of the weaker laundry and registration scores.

## What's Still Broken

The system works for the five questions tested, but there are still areas that could be improved.

First, the project does not currently have `scorer.py`. Because of this, `run_eval.py` could record the generated responses and retrieval distances but could not automatically judge whether each generated answer satisfied a detailed answer-quality criterion.

Second, semantic retrieval occasionally returns unrelated documents. For example, the registration question correctly retrieved `advising_registration.txt`, but dining documents also appeared among the results.

Third, the laundry and registration questions had distances of `0.524` and `0.522`. These passed the `0.6` cutoff but were weaker matches than the housing lottery question at `0.225`.

If I continued working on the system, I would test hybrid retrieval that combines semantic search with keyword matching. Terms such as `laundry`, `registration`, `waitlist`, and specific building names could benefit from keyword matching.

I would then run the same evaluation again and compare the new results with the existing before and after files.

## What I'd Do Differently

I would make the acceptance criteria more specific about both retrieval and answer quality.

For example, instead of only checking whether a question passes the relevance gate, I would define which source should appear in the top results and which facts should appear in the generated answer.

I would also create the evaluation questions earlier and test them while developing the retrieval system. This would make it easier to identify weak retrieval cases before the final evaluation.

I would also add an automated scorer so each run can produce a consistent pass or fail result instead of relying only on manual review.

## How I Used AI

I used ChatGPT to break the project instructions into smaller steps and organize the VS Code and Git workflow.

For Unit 1, I used ChatGPT to help understand chunking, embeddings, retrieval, and the relevance gate. I compared its suggestions with the output produced by my own program.

For Unit 2, I used ChatGPT to help interpret the evaluation output and organize the before and after results. I used my actual terminal output and committed result files as the source for the measurements in this README.

I also used ChatGPT to help identify possible causes of weaker retrieval results, such as retrieval depth, chunking, and keyword matching. I checked these suggestions against the retrieval results before documenting them.

The final measurements, distances, retrieved files, and gate results in this README come from my program's evaluation output.
