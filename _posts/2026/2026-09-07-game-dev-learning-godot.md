---
layout: post
read_time: true
show_date: true
title: "Game Dev: Learning Godot"
date: 2026-09-07
image: /assets/img/posts/2026/20260907/penguin-last-stand-1.png
img_show: false
tags: [Game Dev, Games, Godot]
category: Gaming
author: Strakul
description: "How I got started with the Godot game engine, the small games I built, and what I learned along the way."
toc: yes
---

![](/assets/img/posts/2026/20260907/penguin-last-stand-1.png){: .w-100}
_Title screen for **Penguin's Last Stand**, the most recent game I've finished._

I've always enjoyed playing games, both for the immersive experience and the storytelling aspects. When I learned that making games was mostly coding, that also enticed me as I enjoy coding. I figured maybe someday I'd explore making my own games. This past year and a half I finally gave it a try with Godot: a small, open-source engine with tons of online tutorials, guides, and examples. Here's how I got started, what I built, and what I've learned along the way.

---

## The Inspiration

The main thing that inspired me was a development log. The developers building the [Obojima](https://obojima.com/) TTRPG setting posted a [work-in-progress video](https://youtu.be/hjsx8jETeUc?si=vnX0ZOszDTKij5tF) showing their Godot workflow (that game is still in development, but listed on Steam as [Onaka and the Witch's Intern](https://store.steampowered.com/app/4965590/Onaka_and_the_Witchs_Intern/)). I'm already interested in Obojima, but what stuck with me was the realization that game development is mostly just coding and it's not that hard to pick up. My prior experience with Python very easily translated into Godot's GDScript.

## Learning Godot

I started the way everyone does: a lot of YouTube, getting familiar with nodes, scenes, and signals before I understood why any of them existed. Godot's own [documentation](https://docs.godotengine.org/en/stable/) is pretty good too.

One of the best starting points was a structured course: a [tutorial series by GDQuest](https://youtube.com/playlist?list=PLhqJJNjsQ7KEcm-iYJ2a8UCRN62bTneKa&si=g_sw8AaXYMLQg3XX) that walks you from empty project to a simple finished game. I ran through that, focusing particularly on the 2D lessons since pixel art is the aesthetic I want to make.

There are a bunch of Godot videos on YouTube. Some are short and focused, others ramble on a bit. But you'll be able to find one you like with minimal effort searching. I think the thing to remember is to actually try it out and don't worry too much about the details. Initially I worried a lot about the node/scene structure, but learned to relax a bit and just let it grow as I learn more.

![](/assets/img/posts/2026/20260907/space-journey-2.png){: .w-100}
_**Space Journey** gameplay: defend against incoming ships and asteroids._

## Tools I've Used

There are many tools and resources I encountered as I embarked on this game dev journey. Some of these come from tutorials/recommendations, others from my own searching, or from my own professional work and the tools I use regularly.

- **[Godot 4.7](https://godotengine.org/)**: the game engine itself, coding in GDScript.
- **[Dialogue Manager](https://dialogue.nathanhoad.net/)**: an addon for handling character dialogue, and one example of the many plugins you can install into Godot rather than building the system yourself.
- **[Aseprite](https://www.aseprite.org/)**: making pixel art sprites and tilesets.
- **[itch.io](https://strakul.itch.io)**: where I publish finished demos, playable in the browser. Also a great place for free assets, like [Mystic Woods](https://game-endeavor.itch.io/mystic-woods), [Fantasy Icon Pack](https://ravenmore.itch.io/fantasy-icon-pack), and [Free Pixel Art Forest](https://edermunizz.itch.io/free-pixel-art-forest).
- **[GitHub](https://github.com/)**: source code, typically MIT licensed. Assets usually stay out of the repo since they aren't mine to redistribute.
- **[Claude Code](https://claude.com/)**: debugging errors, refactoring, planning feature implementation. Not writing the game for me.
- **[Zapsplat](https://www.zapsplat.com/)**: royalty-free audio and music (watch out for the licensing fine-print).
- **[Dafont](https://www.dafont.com/)**: free fonts (watch out for the licensing fine-print).

## My Game Dev Journey

Here is how I've put it all into practice over the past year and a half. It's been a bit of an evolution, but the core of it has remained the same: focusing on small systems/games to learn the ropes and get practice.

### 2025

I started around spring or summer of 2025, working through a couple of tutorials and building a pile of small experiments and prototypes. Here are some of the games/systems I published during that time.

**Dice Roller.** A utility program to roll dice. Not really a game, but my first shipped Godot build. [Play](https://strakul.itch.io/dice-roller)

![](/assets/img/posts/2026/20260907/penguin-sledding-2.png){: width="400"}
_**Penguin Sledding**: dodge the trees and rocks as you slide down the hill._

**Penguin Sledding.** My first real game, basically SkiFree from the Windows 95/98 days with a penguin instead of a skier. I implemented a lot of it wrong and it was extremely basic, but it was an experience. I also did my own art here with Aseprite. [Play](https://strakul.itch.io/penguin-sledding) · [Code](https://github.com/dr-rodriguez/Penguin-Sledding)

![](/assets/img/posts/2026/20260907/space-journey-1.png){: .w-100}
_Title screen for **Space Journey**._

**Space Journey.** A Space Invaders style shooter: you fly a spaceship while enemies and asteroids come in from off screen and attack you, so you shoot them first. [Play](https://strakul.itch.io/space-journey) · [Code](https://github.com/dr-rodriguez/Space-Journey)

By that fall I'd lost momentum. I remember having a ton of ideas for **Space Journey**, but having to cut them.

### 2026

I picked it back up almost a year later, around the summer of 2026. All of these projects are deliberately tiny, meant to rebuild familiarity rather than ship anything ambitious.

![](/assets/img/posts/2026/20260907/mini-rpg-1.png){: .w-100}
_Dialogue in **Mini RPG**, handled with the Dialogue Manager addon._

**Mini RPG.** The core loop of a standard RPG with dialogue, inventory, world and battle scenes, and even a visual shader. One new piece for me was [Dialogue Manager](https://dialogue.nathanhoad.net/), an addon I installed specifically for handling dialogue between characters. I didn't use it a ton (I did an unreleased mini project exploring that earlier, though), but it has a lot of promise and I want to use it more in the future. Inventory management was also new to me, and I wish I had done a separate mini project just on that as it was rather complicated.
[Play](https://strakul.itch.io/mini-rpg) · [Code](https://github.com/dr-rodriguez/Mini-RPG)

![](/assets/img/posts/2026/20260907/mini-rpg-2.png){: .w-100}
_The turn-based battle scene in **Mini RPG**._

**Mini Incremental.** My first real simulation loop, and the main thing it taught me: accumulate delta time into a buffer, and every N ticks advance the simulation logic one step. The simulation rate is decoupled from frame rate, so pausing and 2x/5x speed controls are possible and work smoothly. The graphing side I'd read up on in the docs, but a few of the nuances were much faster to solve with AI assistance.
[Play](https://strakul.itch.io/mini-incremental) · [Code](https://github.com/dr-rodriguez/Mini-Incremental)

![](/assets/img/posts/2026/20260907/mini-incremental-1.png){: .w-100}
_**Mini Incremental**: buildings produce and consume resources, with speed controls and a live graph of money over time._

**Penguin's Last Stand.** A top-down survivor game with procedural terrain, endless spawns, and level-up power-ups. Also used custom art this time, with 16x16 sprites and tiles.

Two things were new here. First was seamless infinite terrain. I had poked at some procedural generation back in 2025, so I had something to start with, but watching some videos and asking AI, I implemented a more seamless approach so the map is effectively infinite. There is a repeating pattern, but it's large enough that you are unlikely to notice it. The second was object pooling. The enemies and snowballs come from a pre-allocated pool instead of instantiating/removing nodes. Constantly instantiating and freeing nodes would have been a performance hit (though at my scale of the game likely not noticeable), so it seemed like good practice to learn how to avoid it.
[Play](https://strakul.itch.io/penguins-last-stand) · [Code](https://github.com/dr-rodriguez/Penguin-Last-Stand)

![](/assets/img/posts/2026/20260907/penguin-last-stand-2.png){: .w-100}
_Surviving the endless spawns on procedurally generated terrain in **Penguin's Last Stand**._

## Make Systems, Not Games

**Penguin's Last Stand** took about a month and a half of work.
The first week or two I was very motivated and made fast progress. Then my enthusiasm and my available time both dropped off, and the project sat there for weeks getting maybe an hour on a weekend. What eventually pulled it out was talking about it with friends, which spurred enough interest to spend some extra time and close it out.

Closing it out meant descoping. I had overcommitted on features and had to roll them back. I wanted more power-ups, better art for the player and the enemies, collectible drops that temporarily boost you, alternate attack modes. What shipped is three power-ups and two enemy types. The concepts are in place and expandable, but the wishlist got cut. If I had started with a narrower scope, a smaller game, maybe I would have felt better throughout the whole process.

I remembered a couple of YouTube videos on making systems and not games (from [MMqd](https://www.youtube.com/watch?v=QPuIysZxXwM)) and on focusing on smaller prototypes (from [Juniper Dev](https://www.youtube.com/watch?v=8skhP1U6FNE)). This is really something I should have internalized a bit more and I fell into the trap of making a much larger game. I probably stalled with **Penguin's Last Stand** because I tried to introduce too many complex systems at once.

So in the end, **Penguin's Last Stand** is not amazing and that's fine. It's a short game you can play in five minutes. More importantly, it's done and I wanted that sense of completeness. The systems in it are reusable, so the next game starts from a better implementation instead of from zero.

![](/assets/img/posts/2026/20260907/penguin-last-stand-3.png){: .w-100}
_**Penguin's Last Stand** level-up screen with the three power-ups that made the final cut._

## How I Use AI (And Where I Don't)

When I started, I coded everything myself. Every so often I'd hit something a Google search couldn't fix, and I'd paste the error into a chatbot (ChatGPT, Gemini). It worked, but the model had no access to my files or the wider context of the project, so it was good for quick debugging and not much else. Mostly I relied on tutorials and existing examples instead of having AI write up anything.

Now, agentic coding tools have become ubiquitous and easy to use. They have the whole project in context and my workflow has changed to leverage that. Here's what I do these days:

1. **I write the brief first.** What's my learning goal? What's my realistic level of commitment? What features do I want in the finished thing?
2. **I ask Claude Code for an implementation plan.** Explicitly optimized for the learning experience and the time I can give it, broken into discrete chunks so that every session, or at least every week, moves toward one specific goal.
3. **I ask for a checklist with checkboxes.** I like having visible progress and tracking it. I can open the note, see where I am, and pick up without re-deriving what I was doing.

I could do all three manually. The value isn't that a machine did it, it's that the structure exists at all and that it forces me to think about the project before I write a line of code and commit to what I actually want to learn/practice instead of having an unbounded scope.

In fairness, I never finish all the checkboxes and I add new ones as scope creeps. But I think the scope increase is much less than if I didn't have an outline of what I wanted to work on. So while the project does grow organically, I can see how and keep it in check.

I still write most of the code. AI is for debugging (invaluable, and much better now that it can see the whole project) and for boilerplate or outlines when something is genuinely beyond my current expertise. The pooling implementation in **Penguin's Last Stand** is a fair example: I'd seen it described in videos and the concept is straightforward, but the AI helped me flesh out the actual implementation very quickly.

I also started asking AI to refactor or implement small changes: if I move a variable/function, I'll have it update all references. Or I'll ask it to apply specific formatting or do "busy-work" like repetitive function declarations based on an existing example.

But I draw the line at art and other assets. I don't want AI-generated assets, so I buy or use free packs from itch.io, pull royalty-free audio from Zapsplat, and slowly grind through Aseprite myself.

## Other Things to Learn

### Shaders

I've done the basics and nothing more. There's an excellent [multi-hour walkthrough](https://youtu.be/J9qx8MxuJYM?si=ePi-5sEc2VcAwOX1) that starts with visual shaders and then moves into writing them as code in Godot's shader language.

I started it and want to get back to it, because shaders are hard to reason about (thinking in terms of "what color is this one pixel, with no knowledge of its neighbors" takes some rewiring) and extremely satisfying when they finally click. This is one of the next things I'm hoping to get better at.

### Pixel Art

I'm not an artist by training or by any sense of the word, and it's easy to get stuck on art.
When I'm working on a game, it might not look nice or polished and it's tempting to spend time really improving the art. I've found it best to ignore that feeling. Aseprite time adds up fast, and the result never looks as good as what's in my head, but nothing about it actually blocks the game's core implementation.

Free and purchased asset packs from itch.io are a legitimate way to make a game look decent enough for what I'm doing, and royalty-free audio from Zapsplat does the same job for sound. I use them and I keep going, then draw my own sprites when I feel like drawing sprites. It's rewarding when something comes out okay, but I'm trying not to let it be a blocker for my projects.

## Next Steps

My interest in any hobby waxes and wanes with whatever else is going on, so I can't promise working on this every weekend. But I have a long list of ideas, mostly still exploratory, and maybe at some point one of them turns into something polished enough to be genuinely worth putting out there.

I also want to give Game Jams a shot. These come up fairly regularly on itch.io and other places and I've heard they can be great at keeping one motivated and crunching on a game for a short period of time. My main constraint is my other commitments, but I want to try one and see what I can learn from it.

If you're interested in coding for game development, just try it out. YouTube is a great resource and plenty of people are teaching this well. While I won't be putting out tutorials (no time), I have open sourced my projects, so you can read the actual code on GitHub, and you can play the finished demos on itch.io without downloading anything.
