---
layout: post
read_time: true
show_date: true
title: "Data Science: An AI LLM Wiki for DnD Session Notes"
date: 2026-04-18
image: /assets/img/posts/2026/20260418/graph.png
img_show: false
tags: [Data Science, AI]
category: Data Science
author: Strakul
description: "Comparing LLM wikis, traditional RAG, and frontier models for managing and querying D&D session notes."
toc: yes
---

![](/assets/img/posts/2026/20260418/graph.png)
_Graph representation of notes in the LLM Wiki. Green are labels, red correspond to the character Zinjaro, and blue correspond to the character Soren._

Over the past few weeks, the AI community has been all abuzz about a post from [Andrej Karpathy](https://en.wikipedia.org/wiki/Andrej_Karpathy) about LLM wikis. I've seen a handful of videos about it and decided to give it a try with my DnD session notes. This is an alternative to a traditional RAG (Retrieval Augmented Generation), such as the one I did in one of my prior blog posts—[Data Science: Querying DnD Session Notes with Vector Databases and AI]({% post_url 2025/2025-06-01-data-science-querying-dnd-session-notes-with-vector-databases-and-ai %}). 
While a RAG uses a search engine (a vector database) to retrieve information, the LLM wiki approach uses an AI agent to browse through files like a human would.

Here are the guidelines Karpathy presented: [https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)

The gist of this is that one offloads the organization and querying of a wiki to an AI agent. An agent empowered with a good system prompt and skills can identify relevant documents and synthesize answers without requiring the full setup of a traditional RAG. This is far easier to set up, though it may fail or become prohibitively expensive when dealing with millions of documents (at that point, the RAG approach is superior).

A lot of people have been praising this approach, so I wanted to give it a try. And since I had prior examples of queries against a RAG, I figured I would do the exact same calls and compare those against this local LLM setup and against a Gemini-NotebookLM approach with the full documents.

## Preparing the LLM Wiki

Here are the basic steps I followed:
- Created blank [Obsidian](https://obsidian.md/) project (can choose your IDE of choice)
- Launched [Gemini CLI](https://geminicli.com/docs/get-started/installation/) in that project folder (can choose your own agentic workflow of choice)
- Pasted the link to the LLM Wiki document and asked it to create that
- Iterated on setup, such as tags and folder structure (I like a wiki/sources folder and keeping the raw/ folder clean after ingests are successful)
- Started loading data

My first data load was a bit much. I had downloaded my session notes as a single Markdown file and asked the agent to split it by session date. It worked well, but produced over a hundred Markdown files. Ultimately, this is better, but a single ingest command with all that information took a while and didn't create all the linkages and set of information I wanted. It relied heavily on some spreadsheets I had for characters and locations (which I provided as CSV files), but didn't create character-specific pages until I asked it to later down the line.

For example, I used the following to create character-specific notes:
*Create character wiki notes, similar to that created for @"wiki/Characters/Villhelm Emberstoke.md", for all player characters in Zinjaro's current party: Aolis, Maxim, Brambleberry, Ogra, and also include Thadius and Wobbles. Then populate these wiki pages with information gleaned from the session notes. This is a context-heavy operation, so spin up multiple sub-agents for this, maybe one per character, and provide each with detailed instructions of what it should do.*

![](/assets/img/posts/2026/20260418/subagents.png)
_Screenshot of mult-agent workflow creating character notes._

After some iteration, I got all my notes in place and the wiki prepared with three agent skills: **ingest**, **query**, and **lint**. I haven't used **lint** much yet, but **ingest** and **query** appear to be working well. I did some iteration on the **ingest** since I wanted to add labels and address frequent issues with Unicode characters.

## Query Examples

Used [OpenCode](https://opencode.ai/) with Gemma 4:e4b and 26b_a4b on my personal machine for some of these. This was using [Ollama](https://ollama.com/) with a 32k context window and thinking enabled.
For the Gemini responses, I used the Gemini CLI with Auto (Gemini 3) for the model selection. That introduces some variance, but not much.

In all cases, I explicitly specified to use the query skill and cleared the context between questions. 

I am aware that the local models won't be able to fully compete. The e4b is an edge model optimized for fitting into modest hardware. And the 26b is a Mixture-of-Experts, so the full parameters won't be used for any question. But this is a proof of concept, so I want to see how well these smaller models compare against a frontier model like Gemini.

Let's jump to the questions.
### When did Soren get the most scrolls?

#### Gemma 4:e4b
*Based on the available sources, the documents mention that Soren scribes his scrolls in relation to the content from 2024-08-08.*

#### Gemma 4:26b
*Soren has acquired at most two scrolls in a single session, which occurred on 2024-04-04 (Comprehend Languages and Find Familiar) and 2024-05-02 (Locate Object and Disguise Self).*

#### Gemini CLI
*Soren acquired the most scrolls during the session on August 15, 2024, while exploring a wizard's tower (the tower of Alexander, currently occupied by Alor). During this session, the party found a total of five items identified as scrolls:*  
  
* *Three spell scrolls: Arcane Lock, Glyph of Warding, and Conjure Elementals.*  
* *A fake scroll that contained directions to a book in the "Portals room."*  
* *A Spell Scroll of Demiplane, found later in the session.*  
  
*Soren, the party's Bladesinger Wizard, took these scrolls and was noted to be "scribing his scrolls" in the following session on August 22, 2024. He eventually used the Conjure Elementals scroll to summon an air elemental during a battle on January 9, 2025.*  
  
*Other instances where Soren acquired scrolls include:*  
* *April 4, 2024: Purchased Comprehend Languages and Find Familiar.*  
* *May 2, 2024: Purchased Locate Object and Disguise Self.*  
* *August 1, 2024: Purchased Detect Thoughts.*  
* *September 5, 2024: Purchased Leomund's Tiny Hut.*

![](/assets/img/posts/2026/20260418/gemini_query.png)
_Screenshot of Gemini query response_

#### My Review
Looking at the thinking for Gemma 4:26b, it looked like it was going in the right direction, but focused more on buying scrolls instead of finding them and so missed details (and thus answered incorrectly). It was more complete in its response than the e4b model, which is expected. Gemini using Auto was far better with a lot more detail and relevant context.

### What is Queen Tesselia's connection to Lady Nevermore?

#### Gemma 4:e4b
*I cannot find any connection between Queen Tesselia and Lady Nevermore based on the current content of the wiki or your source documents.*
*If you have any source materials detailing this connection, please upload them to the raw/ folder, and I will integrate the knowledge.*

#### Gemma 4:26b
*Lady Nevermore has several significant connections to Queen Tesselia:*
*   *Regent Advisor: Nevermore serves as a Regent Advisor to the young monarch (Sessions 2024-04-11, 2024-04-11).*
*   *Protector/Ally: Nevermore has been involved in protecting Tesselia, such as when a carriage carrying the Queen was being moved to her safehouse (Sessions 2024-03-21). She also recently assisted in freeing Tesselia from a mechanical cage (Sessions 2024-04-04).*
*   *Personal Connection: Nevermore was acquainted with Tesselia's mother and made a promise to her (Sessions 2024-04-04, 2024-04-11).*
*   *Shared Interests: They have been seen together in various contexts, including with the Sofia AI (Sessions 2024-08-01).*

#### Gemini CLI
*Queen Tesselia and Lady Angela Nevermore are key political figures in the kingdom of Andore, specifically within the city of Tessalia. Their connection is primarily one of alliance against a common enemy: the Regent, Ashton Whitemore.*  
  
*Key Points of Connection:*  
* *Political Alliance: Lady Angela Nevermore is a human noble who seeks to lead a coup d'etat to overthrow the Regent, who is Queen Tesselia's father. While the Regent is plotting to kill the young Queen to take full control, Lady Nevermore works to oppose him and take over the regency herself.*  
* *Direct Interaction: During a session on April 18, 2024, Lady Nevermore visited the party accompanied by a person in disguise. She used an alert spell to drop the disguise, revealing it was actually the 7-year-old Queen Tesselia.*  
* *Guardian/Ally Role: Lady Nevermore appears to act as a protector or co-conspirator for the Queen, facilitating her secret movements and helping her evade the Regent's influence.*  
  
*This information is derived from the Characters list and Session: 2024-04-18.*

#### My Review
This is always a hard question since Tesselia is both the name of the city and the name of the person, and while Nevermore appears to be a villain initially, she's actually trying to help the young queen. Gemini was best able to capture the subtleties of their relationship and history.

### How and when did Tririn die?

#### Gemma 4:e4b
*Tririn died on the date linked to the events described in the session log wiki/Sessions/2025-01-16.md and its source wiki/sources/2025-01-16.md.*
*Specifically, the records indicate that Tririn dies after Soten drops to 1 HP during the sequence of events, which includes a Time Stop.*
*Relevant sources include:*
*   *wiki/Sources/2025-01-16.md (Line 33): "Soten drops to 1 HP, Tririn dies."*

#### Gemma 4:26b
*Tririn died during the session on 2025-01-16. Following a Time Stop effect, both Tririn and Soten were required to make Dexterity saving throws at disadvantage; while Soten survived with 1 HP, Tririn died.*

#### Gemini CLI
*Tirin Bronzepride died on January 16, 2025, during a climactic battle against the Athena AI (Session 2025-01-16).*  
  
*He had already fallen unconscious earlier in the combat. As the facility began to collapse and the party attempted to retreat toward an elevator, a Time Stop spell was cast. Following the spell's conclusion, Tririn was forced to make a Dexterity saving throw at disadvantage—likely due to his unconscious state and falling rubble—which he failed, resulting in his death.*

#### My Review
All models were able to identify the correct date and certain aspects of the event, with varying levels of detail.

### How did the party invent Christmas?

#### Gemma 4:e4b
*The current wiki and source materials do not contain information detailing how the party invented Christmas. I found only mentions of "Cherish Eve (equivalent of Christmas)" and general mentions of "Christmas" in session logs, but no synthesis of its invention.*

#### Gemma 4:26b
*The party "invented" Christmas (known in their world as Cherish Day) during the session on 2024-08-22. Following a story about Santa told by Nova, the group staged a festival where:*  
* *Soten and Soren were dressed as helper elves by Tririn.*  
* *Juneberry was polymorphed into a reindeer.*  
* *Soren used Floating Disk to carry the group.*  
* *The party traveled through the shops tossing presents to the townsfolk.*

#### Gemini CLI
*The party invented Christmas by rebranding the existing holiday of Cherish Day during their visit to Wheat Burrows in August 2024.*  
  
*Inspired by Nova's stories of Santa Claus, the party orchestrated a festive performance:*  
* *Nova and Shar served as the storytellers, sharing the legend of Santa with the town.*  
* *Tririn outfitted Soten and Soren as "helper elves."*  
* *Juneberry used Polymorph to become a reindeer.*  
* *Soren cast Floating Disk to create a magical sleigh, which the "elves" rode while tossing presents to the townspeople.*  
  
*The event was so successful that it became a lasting legend in Wheat Burrows. I have created a new wiki page, The Invention of Christmas, and updated the index to preserve this piece of party lore.*

#### My Review
The Gemma 4 e4b model completely failed to capture the meaning of the question. It was on the right track, but did not catch that it should have synthesized the information about Cherish Eve. The 26b model got it right and the thinking suggested it wanted to create a note to preserve this but didn't do so. It's possible a follow up prompt would have caused it to try this. Gemini got a great answer including the name of the town which I had forgotten and actually created and indexed a wiki page to preserve this information.

### Why is the party journeying north from Helines? What is their quest and who gave it to them?

#### Gemma 4:e4b
*The logs and indexes do not contain specific information about the party's quest, nor do they contain details on the person who gave them the quest in conjunction with them traveling north from Helines. For more in-depth knowledge, I would recommend checking the historical session logs, particularly those from the region of Helines or the immediate area where the journey takes place, as the query might have been covered in a prior session's narrative.*
*Based on the available wiki data, the query related to the party's journeying north from Helines, their quest, and who assigned it is not found in the main index or location files.*
*Please specify a session date or narrow down the conversation, and I can perform a deeper search of the relevant session logs. For example, I could search for:*
*   *Specific characters involved with the quest.*
*   *The general quest objective or associated keywords.*
*   *The timeframe when the quest was given or the travel occurred.*

#### Gemma 4:26b
*The information provided in the wiki does not explicitly state why the party is journeying north from Helines or who gave them their quest.* 
*The notes confirm that in the session on 2025-01-08, Aolis teleported the party to Helines to report back with Mayor Maxwell, and there was an "upcoming big mission" mentioned, but the specific details of the quest's origin and purpose for heading north are not present in the available documents.*

#### Gemini CLI
*The party is journeying north from Helines to Drakenweld for several reasons:*  
  
1. *Finding Mayor Maxwell: The primary motivation for the journey was that Mayor Maxwell of Helines went north to Drakenweld and failed to return. His brother, Lucious, was left as acting mayor in his absence.*  
2. *Investigating Ancient Technology: The Adventurer's Guild tasked the party with investigating ancient technology and the Net (a planar wall) in the northern region. This mission is known as the Drakenweld Excursion.*  
3. *The Demon Threat: A giant portal opened in the Drakenweld mountains, releasing demons and threatening the region.*  
*This has complicated a pre-existing treaty with the local dragons.*  
  
*The quest was given to the party by Lady Poomf, the bird-like Guild Leader of the Adventurer's Guild in Helines. She is their primary quest-giver and the one they report back to regarding the guild's interests.*  
  
*I have created a new wiki page, Drakenweld Quest, to synthesize this information and have updated the wiki index accordingly.*

#### My Review
This is a particularly tricky question as it isn't super well worded. It very easily tripped up both local models. The e4b model failed to find anything and the 26b model instead found the return trip via teleportation. The Gemini model, however, correctly found the primary reason (finding Mayor Maxwell) and also found ancillary information that was revealed in lore later on (the ancient technology and demon threat). That wasn't an original quest item, though. Lady Poomf was indeed the quest giver. Gemini again wanted to create a note, which I allowed for future searches.

## Additional Comparisons

In addition to the queries against the local LLM, I also ran them in a Gemini Notebook, with sources loaded into NotebookLM in the form of Google Drive documents. I ran the queries using the Gemini 3 Fast mode and they tended to be very good. I actually was surprised as it mentioned things I had completely forgotten about. It also included explicit citations to the results it matched, which made verification easy (the local LLM Wiki tended to also provide references, but not consistently). The Gemini-Notebook responses, though, were generally more verbose, like the answer to getting the most scrolls sort of listed nearly every one in a fancy table instead of giving the answer of the August date.

I also re-ran my old RAG setup (see details [here]({% post_url 2025/2025-06-01-data-science-querying-dnd-session-notes-with-vector-databases-and-ai %})) with the newer Gemma 4 models. Unfortunately, I hadn't saved exactly what parameters I used so I used the default setup. For better comparison, I also re-ran the default setup then with the Gemma 3:12b model I had used before, with a 32k context window. These results surprised me as they were widely variable. Sometimes they were spot on and rivaled the Gemini model, but most of the times they were extremely incorrect. In this case, the issue is that the RAG is dominated by the matched documents and my implementation is too simplistic. The wrong session notes were being used and there was no way for the model to know additional information existed so it answered based on just that input.

Finally, I also re-ran the Gemma 4:e4b calls with a larger 128k context window. It performed better a few times, but not consistently. For example, the scroll question was very poorly answered, but it was actually able to better answer the Tesselia question. Because this was done after some of my Gemini queries, it did benefit from the wiki updates that model did (such as the Christmas note). OpenCode reported using about 12k context, so increasing the window to 128k may not have been needed.

I've tabulated the results of my queries, and included the comparison answers from the RAG way back, into a Google Sheet I'm making publicly available [here](https://docs.google.com/spreadsheets/d/1unSs8YxWY2jRTZMvRPP7FuDpClfSJeqn6wjAfq5foK4/edit?usp=sharing).

## Summary

I was impressed most by the Gemini CLI responses using the local LLM Wiki. I felt I had more control over the context and the response length than the web app (Gemini-NotebookLM), although the web app required barely any setup. I also liked that it was accurate, to the point, and created additional wiki notes for future lookups. The Gemma 4 e4b model consistently failed to find relevant information, which was disappointing. I think this type of model might benefit more from a more traditional RAG approach. The Gemma 4 26b model did better, but wasn't perfect either. That one was noticeably slower since it doesn't fit into my GPU. Simple, direct questions can be handled well by those local models, but if any synthesis is needed across multiple notes it can't quite handle it. 

I was worried about the context length being used, but was surprised at how variable it was. The Gemma 4 26b was typically around ~11-18k tokens, part of which may be OpenCode's system prompt, plus the agent instructions and skills. That's uncomfortably close to the ~50% mark of my 32k context window so it could have impacted accuracy. I did test the smaller e4b model with a higher context, but it wasn't always better.
On the other hand, Gemini CLI reported sometimes a context length of 580k, sometimes more. It's no surprise it was able to answer better if it was actually using nearly 50 times more context than the local models! This was with me actively clearing between sessions, but suggests if you are paying per token (which is common for API usage) this will heavily eat into your quotas.

My RAG re-run comparisons were more token-light and returned results faster, but suffered if the matched documents weren't relevant. A good RAG is hard to set up as there may be multiple approaches to take in determining what are the best documents to supply the model with. My default setup failed to find good matches for some of the queries and got extremely incorrect answers. The LLM wiki works around that situation by requiring the model to do the matching via direct searches. As long as it has a good context window and proper skills it can do a good job with that.

Overall, a local LLM wiki was easier to set up than a RAG but may be of limited use. It feels much more token-heavy, which can be a limiting factor for some, and to really benefit you do want to use a better model. If you have thousands of documents, the RAG would be better, but for simple hobby projects like these, it may be a good alternative to the complexity that using a RAG introduces. 

If you'd like to try out this LLM Wiki setup, you can grab a copy of it from Github [here](https://github.com/dr-rodriguez/dnd-llm-wiki).
