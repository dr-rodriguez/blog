---
layout: post
read_time: true
show_date: true
title: "Data Science: Publishing My DnD LLM Wiki with Quartz"
date: 2026-10-11
image: /assets/img/posts/2026/20261011/dnd-home.png
img_show: false
tags: [Data Science, AI, TTRPG]
category: Data Science
author: Strakul
description: "Turning my LLM-managed DnD session wiki into a public website with Quartz, custom CSS, and GitHub Actions."
toc: yes
---

A few months ago I set up an [LLM wiki for my DnD session notes]({% post_url 2026/2026-04-18-data-science-llm-wiki-dnd-session-notes %}): a folder of markdown files that an AI agent organizes, links, and queries for me. It works great, but it lives in a GitHub repo full of raw markdown, which isn't exactly something I can hand to the rest of my party. So the next step was to figure out how to share this more easily, perhaps by hosting it as a website.

The result is live at [strakul.com/ttrpg-terra](https://strakul.com/ttrpg-terra).

![](/assets/img/posts/2026/20261011/dnd-session-dark.png){: .w-100}
_A wiki session page in dark mode._

---

## The Workflow

I had originally looked into World Anvil. It's a good service, but a little clunky to use, full of ads, and doesn't make it easy to bulk transfer all my notes.

The whole point of this is that I don't want to maintain a website. I just want to take notes and have the rest be automated. Here's my loop:

1. I take notes during our sessions while everyone is off doing various things.
2. I have Claude Code rewrite those notes in the style of the wiki (character pages, locations, session logs, and all the cross-links between them).
3. I push the wiki notes to GitHub.
4. The website rebuilds itself automatically for everyone to enjoy.

Step 4 is the new part and that's where Quartz and GitHub Actions come in.

## Why Quartz

[Quartz](https://quartz.jzhao.xyz/) is a static site generator built for publishing Obsidian-style markdown. It handles `[[wikilinks]]`, embeds, backlinks, graph view, and search out of the box, which is basically everything a campaign wiki needs. It even ships with a TTRPG template, so I started there. I'd already been moving my notes into Obsidian (see [LLM Wiki Notes]({% post_url 2026/2026-04-18-data-science-llm-wiki-dnd-session-notes %})) so this was a natural choice.

Honestly, the default setup did most of the work. My changes on the Quartz side were mostly flipping features on and off in the config, for example:
- **Excalidraw:** Disabled. I'm not using it, and leaving it on just produced warnings during the build.
- **Recent notes:** I toyed around with it, didn't like how it looked, and turned it off.
- **Comments:** Quartz supports a comment section, very similar to what I have on the blog. I decided against it. I don't expect this site to get a lot of traffic, so there's no real need.

There's other functionality that I didn't explore, like private pages, password protection, etc. Not really something I use. But the Base tables did migrate beautifully. The individual views are preserved and you can sort by columns.

![](/assets/img/posts/2026/20261011/dnd-character.png){: .w-100}
_The character Base table on the wiki._

## Fixing the Wiki

The biggest aspect to this was that most of the edits I needed weren't in Quartz at all. They were in the wiki pages themselves. Things that looked fine as markdown files in an editor looked a bit off as a website. For example:
- The index page needed an actual title, since it's now the homepage.
- Some of the character pages needed reorganizing so they read better as standalone web pages.

This makes sense in hindsight. When an agent writes notes for itself (or for me browsing in Obsidian), the structure doesn't need to be polished. Once real people are reading it in a browser, presentation matters a lot more.

One aspect that I decided against supporting are the raw pages. Those are part of the GitHub repo but not built on the website. Links to those will fail, which is expected, but I just wanted to show the more polished wiki notes. If you really wanted to see the raw notes, you can go to the wiki repo itself.

## The Look: Vibe-Coding CSS

The main visual element was the custom CSS. I've worked with CSS before, so it's not new to me, but I didn't have the time (or patience) to hand-tune a full theme. 
Instead, I browsed some free template HTML websites until I found one I liked: old-time fonts and a sort of Renaissance-style page look that felt right for a fantasy campaign; specifically [The Wendmere](https://similar-octopus-500582.framer.app/).

I pointed Claude Code at it and asked for CSS that captured the same feel: similar fonts, similar colors, similar styling. I was explicit that it should *not* grab the actual images or any other assets from that site. I just wanted the style, not a copy.

![](/assets/img/posts/2026/20261011/dnd-home.png){: .w-100}
_The wiki in light mode, with the custom Renaissance-style theme._

The end result is what you see on the site now. For something that took a fraction of the time it would have taken me to build by hand, I'm pretty happy with it. I even learned a few things: I didn't know one could control the styling of the first paragraph/letter this way with just CSS.

Here's the snippet for that:

```css
// Illuminated drop cap on the first paragraph of a page
article > p:first-of-type::first-letter,
article .markdown-rendered > p:first-of-type::first-letter {
  float: left;
  font-family: var(--headerFont);
  font-weight: 700;
  font-size: 3.4em;
  line-height: 0.9;
  color: var(--gold);
  background: var(--lapis);
  border: 3px double var(--gold);
  outline: 1px solid var(--dark);
  padding: 0.12em 0.16em 0.04em;
  margin: 0.12em 0.45rem 0 0;
}
```

The `:first-of-type` pseudo-class picks out the first paragraph and `::first-letter` targets just its opening character, which is then floated left and styled like an illuminated manuscript initial. The `--gold` and `--lapis` colors are custom variables defined elsewhere in the stylesheet.

## Deployment: GitHub Actions and a Dispatch Trigger

Hosting is through GitHub Pages, which is the exact same setup as this blog. My main GitHub Pages repo points at strakul.com, and any other repo with Pages enabled is automatically served at `strakul.com/<repo-name>`. So there was almost nothing for me to do beyond picking a reasonable-sounding repo name (hence `ttrpg-terra`).

There are two repos involved:
- **`dnd-llm-wiki`:** The core wiki, where all the notes live, including raw notes.
- **`ttrpg-terra`:** The Quartz site, which checks out the wiki's `wiki/` folder at build time and builds from it.

Quartz provides its own GitHub Actions deployment workflow. I needed to make some edits to it (mainly adding that second checkout of the wiki repo), which I had Claude Code help with.

The new piece for me was the **dispatch** trigger. Normally the site would only rebuild when the Quartz repo itself changes, but the notes live in a different repo. To fix that, the wiki repo has its own small action: whenever I push changes to the wiki, it sends a `repository_dispatch` event to the Quartz repo, and that triggers the rebuild.

{% raw %}
```yaml
name: Trigger site rebuild

on:
  push:
    branches:
      - main
    paths:
      - "wiki/**"
  workflow_dispatch:

permissions: {}

jobs:
  dispatch:
    runs-on: ubuntu-latest
    steps:
      - name: Notify ttrpg-terra
        env:
          GH_TOKEN: ${{ secrets.QUARTZ_DISPATCH_TOKEN }}
        run: gh api repos/dr-rodriguez/ttrpg-terra/dispatches -f event_type=wiki-updated
```
{% endraw %}

I hadn't used this before, but it was relatively easy and useful to know about. The only extra step is a fine-grained access token stored as a secret in the wiki repo, since the default workflow token can't trigger actions in another repository.

## Final Thoughts

This is really a proof of concept: can I go from messy session notes to a published, browsable wiki with almost no manual effort? Yep! Between the LLM wiki doing the organizing, Quartz doing the rendering, and GitHub Actions doing the publishing, my only job is to take notes and ask the agent to process them.
There's room to expand, but none of that is necessary right now. This is exactly what I wanted from this little hobby project.
