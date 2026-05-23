---
layout: post
read_time: true
show_date: true
title: "Data Science: Querying DnD Session Notes with Vector Databases and AI"
date: 2025-06-01
tags: [Data Science, Databases, Python, Programming, AI]
category: Data Science
author: Strakul
description: ""
---

Last week, we had some guests at work and one of them presented a multi-agent model for creating database queries with some clever documentation retrievals. It was very exciting and something I've wanted to do, so I decided to take some steps and familiarize myself with some of the concepts. I learn best by practicing, though, so I set out to do a simple hack day project: a way to create a retrieval augmented generation (RAG) application that would take some notes I have from our multi-year Dungeons and Dragons (DnD) campaign and generate appropriate responses when passed through an AI large language model (LLM). 

Let's go through what I ended up building.   
You can also follow along by looking through the GitHub repo for it: <https://github.com/dr-rodriguez/ollama-ttrpg-query>

### The Tools

Before starting, I wanted to identify what tools I should use. I'm already familiar with running local LLMs with Ollama so I stuck with that. **Ollama** is a straight forward download at <https://ollama.com/> and allows you to pull a variety of models. I ended up using gemma3:12b given my GPU (an RTX 4060) has 8GB of VRAM. You can find more information on the Gemma3 models at <https://ollama.com/library/gemma3>

I was very pleased to see the context window size for Gemma3 is 128k tokens, which makes what I'm planning to do much easier. For the uninitiated, this is how large of a prompt you can supply to an LLM. Anything larger gets truncate. A good rule of thumb is that 4 characters is about 1 token, or about 3/4 of an English word. So this amounts to ~96,000 words which is sufficient for my purposes

The other big tool is the database that will store the RAG. For this I use **ChromaDB** , which can quickly build up a vector database. You can find more information on it at <https://docs.trychroma.com/docs/overview/introduction>

The idea behind a vector database is that it implements a mathematical representation of the underlying documents. This is optimized for nearest-neighbor retrievals. That is, you can find documents that are similar to one another. 

### The Plan

The first order of business was to get my notes down from where I store them, in Google Docs. I spent some time reading about the Google Docs API, but gave up on it- I just downloaded a plain text representation.

I wrote a simple python application that would read and split it up as a list of documents. Each of my session notes starts the same way- with the date so I was able to split up my notes in a list. There were about 65 session notes over the span of nearly 2 years so it's a good sample. Not all notes are equal though, some are very brief, others have a lot of background, and yet others are reminders to myself of character progression ideas. Still, this represents a moderately sized set of notes. In total, it's about 22,000 words which could be small enough to fit entirely in an LLM's memory, but I wanted to explore the vector database approach. The code to read the file and split it into documents is in [read_file.py](https://github.com/dr-rodriguez/ollama-ttrpg-query/blob/main/read_file.py).

To build the vector database, I took each set of session notes and ran it through an embedding model. You can read a tutorial on that here: <https://ollama.com/blog/embedding-models> Essentially, I converted a session's notes into a numeric representation based on its context. I ended up using the mxbai-embed-large model to compute these values. I tried nomic-embed-text as well, but that one did not end up being as accurate with my brief tests. This database is generated in the [prepare_rag.py](https://github.com/dr-rodriguez/ollama-ttrpg-query/blob/main/prepare_rag.py) file.

For the LLM, I stuck to using Ollama's python API and used the example above on dealing with the embedding models. The gist of it is that I would take a user question, apply the same model embeddings to identify sessions that seemed relevant using the vector database, and then create a final LLM prompt that combined the session notes, the initial question, and some extra guidelines. This would be sent to gemma3 and the response would be returned.

This happens in [query.py](https://github.com/dr-rodriguez/ollama-ttrpg-query/blob/main/query.py) and an example final prompt for a question like "Who is Soren?" looks like:

> Using the notes from these sessions: 
> 
> [START NOTES FROM 2024-06-06] 
> 
> (session notes would go here)
> 
> [END NOTES FROM 2024-06-06] 

> (additional notes would be added here) 

> Answer this prompt: Who is Soren?, including references to the session notes. 
> 
> Attempt to be concise if possible. If you don't have enough information- say so and avoid making assumptions. 

### The Execution

To make things easier, I wrote query.py so it could be executed from the command line. That makes the process of testing it out much simpler. It does take a while to get back a response, not too long though the first time ollama loads the model always takes a bit. Querying the database is very fast given the small number of documents. 

Overall, I found that identifying the right documents was sometimes tricky. Because my notes are sometimes inconsistent or sparse, a match may not be found for a particular query. Or there may be things I remembered but never fully wrote down. That's fine- it's part of the limitations of using notes like this, but it does mean I sometimes need more than one session note to fully capture the relevant information.

One thing I did to alleviate the limited nature of the notes was to append to the notes the prior and future session notes for any given match. So if I match the notes for week 12, I would automatically get the results for week 11 and 13 as additional context. This helps ensure continuity, particularly when our sessions were cut short or something like a battle spanned over two sessions.

Through the command line, you can control how many sessions to search and whether or not to include the extra context notes. This can be seeing with query.py --help:

> usage: query.py [-h] [-n NUM_NOTES] [-d] prompt
> 
> Query the DnD notes for relevant information.
> 
> positional arguments:
> 
> prompt A required string prompt for the program.
> 
> options:
> 
> -h, --help show this help message and exit
> 
> -n NUM_NOTES, --num-notes NUM_NOTES
> 
> An optional integer specifying the number of notes (default: 2).
> 
> -d, --disable-extra An optional flag to disable fetching extra notes from prior/future sessions (default: False).

With that, we have everything in place so let's see how it works. 

### Examples

I ran a bunch of questions to see how things worked. It really depends most on the quality of my notes, but also on the nature of the questions. Some things, like the slow reveal of an NPCs backstory will be hard to query on, but a single event is easy to get a handle on. 

The AI does sometimes hallucinate or garble up the session notes. For instance, our campaign switched a bit after Chapter 2 with new characters and new settings in Chapter 3. If sessions match between the two chapters, the AI could incorrectly assume they are related when likely they are not.

Let's go over some of the questions I asked:

## When did Soren get the most scrolls?

Soren is my character, an aarakocra (bird-folk) wizard. Here's what the AI had to say about this prompt:

> Based on the provided session notes, the period when Soren acquired the most scrolls was between August 8th and August 15th, 2024.
> 
>   
> 
> 
> Here’s a breakdown:
> 
>   
> 
> 
>   * August 2nd, 2024: Soren bought a scroll of Detect Thoughts (2nd level) for 200g.
>   * August 15th, 2024: While examining the tower with Alor, several scrolls were discovered, including: Arcane Lock, Glyph of Warding, Conjure Elementals, and a Spell Scroll of Demiplane. This implies a significant influx of scrolls during this session.
> 

> 
> Therefore, the window between August 8th and August 15th saw the most scrolls acquired by Soren.

This is actually not bad. I do think the exploration of the wizard tower was time Soren got the most scrolls. The session before I think he scribed a few extra as well, though that's not in the bullet points.

## What is Queen Tesselia's connection to Lady Nevermore?

Tesselia and Nevermore are prominent NPCs in our campaign and their relationship is revealed over multiple sessions. Here's the AI response:

> Based on the session notes, Queen Tesselia and Lady Nevermore have a complex and developing connection, hinting at a deeper history and shared purpose. Here’s what we know:
> 
>   * Acquaintance: Nevermore is familiar with Tesselia's mother ("She knows the 'being' in the device that held Tesselia. Knows 'secret codes'"). This suggests Nevermore knew Tesselia's mother before Tesselia’s current age.
>   * Shared Secret: Their joint exploration of the ancient facility beneath the castle, facilitated by Tesselia using a code (“TESSELIA”), implies a shared understanding and mission related to the site and its technology.
>   * Potential Protector/Advisor: Nevermore has taken on the role of “Regent Advisor” to Tesselia, indicating a protective and guiding role.
>   * Unclear Depth: While there’s a clear connection and shared knowledge, the exact nature of their relationship (whether it's mentorship, familial, or something else entirely) remains unclear. The "secret codes" Nevermore knows about Tesselia's mother are a major hint, but we don's know what that implies.
> 

> 
> It's possible their relationship is significantly deeper than we currently understand, but the notes do not provide sufficient information to definitively state their connection.

This is also pretty good. I think a few other notes may have been needed, or my notes be clearer, for it to do better.

## How and when did Tririn die?

Tririn was one of our player characters, a barbarian lion-folk. Here's what the AI had to say for this question:

> Tririn died during the final confrontation with Athena in the high-tech facility (referenced in the 2025-01-16 session notes). Specifically, after a period of sustained attrition against Polythemis and his cultists (2024-12-05 session notes) and facing a significant influx of robot adds and Athena’s defenses (2025-01-09 session notes), Tririn succumbed after failing a Dexterity saving throw at disadvantage following a Time Stop spell cast during a desperate attempt to escape the collapsing facility.
> 
> There's no additional information provided to determine the precise cause of death beyond that.

This is accurate. We had some tough fight with Polythemis and then moved to the big boss fight against Athena. It was at the end of the fight that he died though I just wrote down "Tririn dies" in my notes so that's why the AI can't provide anything beyond this.

## How did the party invent Christmas?

This was a fun little side quest where we ended up in a small town celebrating the local "Cherish Eve" and decided we would make it like an actual Chrismas celebration. Here's what the AI said:

> The party essentially invented Christmas for the town, leveraging Nova’s innate showmanship and their accumulated magical abilities. Here's how, based on the notes:
> 
>   1. Nova's Idea: Nova, known for his spectacle, proposed a Christmas celebration, likely inspired by the concept of "Santa" (which he shared with the town, suggesting a prior knowledge of this tradition).
>   2. The Spectacle: They combined Nova's performance (wrestling a Brown Bear pulled from a Rust Bag of Tricks – a chaotic but entertaining start) with Soren’s lute playing and Shar and June’s collecting money, effectively turning the event into a festival.
>   3. Magical Transformation: Soten dressed the party (and themselves!) as elves, and June was polymorphed into a reindeer. Soren conjured a Floating Disk for transportation, creating a whimsical, magical parade.
>   4. The Presentation: Nova, with Shar's help, told the story, completing the festive atmosphere. The successful, memorable nature of the performance ensured the town would “speak about this for years.”
> 

> 
> While the notes don't specify a pre-existing holiday tradition, the party clearly crafted a unique and impactful celebration, which the townspeople likely adopted as their own version of Christmas.
> 
> Missing Information: The notes don’t explain where Nova learned about “Santa”, nor what the original Christmas tradition looked like.

This took a few iterations to get something decent. For example, it sometimes said that the town was Helines which was a city from Chapter 3 whereas this took place in Chapter 2. To address that I asked it to do only a single session match (and use the prior/next for context) and that worked better. Overall, this was pretty accurate.

## Why is the party journeying north from Helines? What is their quest and who gave it to them?

This is the start of the major plotline in Chapter 3. The new group has set north from Helines. Here's the AI response:

> The party is journeying north from Helines to find the missing Mayor Maxwell. Lady Poomf informed them of the Mayor's disappearance at a dig site (2025-02-13). The Mayor went north, possibly towards Drakenweld (2025-02-13), and it’s believed he may have some magical training (2025-02-13).
> 
> The party’s quest is to locate the Mayor, although the exact nature of his disappearance and potential dangers involved are unclear.
> 
> Information gaps: It is unknown if the party is seeking the Mayor on behalf of someone else, or if they have a specific agenda related to his whereabouts beyond simply finding him.

This is very accurate- it was indeed Lady Poomf who told us at the dig site to go search for Mayor Maxwell and we indeed do not know all the details. 

### Summary

Overall, this was an interesting exercise and did not take too long to build. I would summarize this as a fairly basic AI-powered search engine for my DnD notes. It can certainly do better, but part of that is my own note-taking while part is limitations with the AI. 

Given the size of the notes I could probably have sent the entire thing in the prompt, but likely would have had more hallucinations or incorrect associations. This approach kept the LLM focused on just the main context that I felt was relevant for the task at hand.

One thing that did occur to me was having an extra step of validation. For example, send each set of notes to an LLM first to have it determine if it is relevant to the question or now and then filter the response accordingly. That would take a bit more work and it's not clear that it would improve things, but it's a way of introducing a multi-agent approach to this to fine-tune the output.

In the end, I'm satisfied with how this worked out and will probably continue to update the GitHub repo with my notes and further examples as I come up with them. I also now have some plans for further explorations of vector databases, independent of the AI context. 
