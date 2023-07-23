---
title: "Changing the way I make notes"
date: 2023-07-23
series: 
  - Musings
tags:
  - Logseq
  - PKM
TocOpen: false
draft: false
---
I've been exploring [Zettelkasten](https://zettelkasten.de/) a lot. Reading tons of articles for inspiration and understanding. And recently I stumbled upon [Jay Colbert's Zettelkasten workflow](https://wilde-at-heart.garden/pages/my-zettelkasten-workflow-from-start-to-finish/). Inspiring change and this blogpost.

I have noticed an increase in notes I need to dig up and work on more at a later time.  
And even though zettelkasten isn't the way forward, Jay's article gave me the ideas required to find a workflow for myself.

In this blogpost I will show you the simple system I made for myself based on Jay's article. Reading his article will give extra context, but I will do my best to not make it required reading for this one.

## Zettelkasten vs. my notes
The zettelkasten system as I have come to understand it has three categories of notes: fleeting notes, zettels (a.k.a. main notes, or sometimes permanent notes) and reference notes (a.k.a. literature notes).

A lot of my notes would fall in the category of reference notes. I love collecting information.
Otherwise my notes are in the space between fleeting and zettels.

I interpret fleeting notes as short term, small notes to work on later. Whereas zettels are self contained ideas (a.k.a. atomic) to keep in your system permanently.  
My notes are often multiple related ideas. Or one big, complex idea. They may actually be "done" in contrast with fleeting notes. Which means, they are fleshed out and have reached a conclusion.

## Changing my workflow
In Jay's article he explains that every note starts in the Logseq Journal pages with a specific template for that note type. I liked the idea of this approach the moment I read it.

The only thing I had to figure out now, was what that would look like for my notes.

When I moved over to my new system I had gathered 42 notes as part of my Personal Knowledge database. I feel this is a nice amount to experiment with. It provides enough bulk to see if and how a set up works. And there aren't too many notes to put in a different set up.

I knew from the start that atomic notes, or zettels, weren't going to work for me. It's just not how my brain operates in day-to-day life.  
So my focus was on morphing the idea of fleeting notes into something that works for me.

## Fleeting notes
The notion that "everything is a fleeting note", doesn't quite work well for me. The note that states "today I played World of Warcraft" isn't really a thought that needs further processing. That's just what I did that day.  
Jay makes this distinction as well. Using a template for those fleeting notes to be processed more in the future. He writes,
> In Logseq, I have a template for the fleeting notes I know will feed into my Zettelkasten. Usually, these are ideas for Zettels.

As my notes are not quite fleeting, my first step was defining a better term to use.  
On his website Jay explains that he uses his Digital Garden for divergence and his Zettelkasten for convergence.[^1]  
My notes are often both. First I brainstorm and they diverge, then I refine the note to be more concise and to the point. This is what I did to contemplate my new set up. Brainstorming what my notes were for, what structure they followed. And then organizing the note so it is readable and I can draw conclusions from my brainstorm.  
This helped me find the term best suited for what I am actually doing: exploring. I am exploring my thoughts as I write them down.  
Because I make an effort to keep my graph in my native language (Dutch) as much as possible I translated that to `🧭verkenning` (I like icons!).

## Making a template
For the template, I rearranged and changed Jay's fleeting note template.

This is what his template looks like.
```
- TODO **Note goes here**
  date::
  tags:: 
  type:: [[fleeting]]
```

And here's what I made of it for myself.
```
- 🧭 Notitie titel
  template:: verkenning
  soort:: [[🧭verkenning]]
  onderwerp:: 
  stadium:: 🌱🌿🌳
  updates:: <%today%>
```

I changed `type` to `soort`. It's a synonym as type is also a Dutch word, but I like the word soort better in this case. 

I use `onderwerp` instead of `tags`. Onderwerp is the Dutch word for subject. I choose this to indicate the purpose of the property more clearly.

I like the growth analogy from Digital Gardens, to indicate the maturity of my exploration. So I added the property `stadium`, Dutch for stage.  
I have three stages indicated by an icon. A seed for my initial thoughts, a branch for a thought that is being worked on and a tree for a thought that reached a conclusion.  
It also serves as a replacement for the `TODO` Jay uses. I already use `TODO` for my task management.

In Jay's workflow the property `date` is used. As all notes live on a journal page this doesn't add value for me. Jay says it's about sorting. Using advanced queries I get the journal-day attribute for sorting (it's formatted as `YYYYMMDD`).  
What I do want is the ability to refine my note. So I don't want to use references and embeds, I want to change the note itself. And I want to see that I changed it.  
I decided on the property `updates`. In updates I will put all journal page links for dates that I worked on the note. I feel the Dutch word "aanpassingen" is too long, so I kept this one English.

Finally, I put a title at the top and then use indented bullets underneath this template to further explore my thoughts.

## In Practice
Turns out that only 6 of those 42 notes I mentioned actually fit the "exploring" category of notes. The vast majority are references, as established, but a sizable amount was neither.  
When I went through each of my notes, reforming them to fit my new template, 14 notes felt unfit for this new template. I quickly noticed a pattern in my note taking when doing this exercise. I wasn't just exploring my thoughts, I was also noting down life lessons.

Time for a new template then. These life lessons don't need to be explored, but they do need to be collected in some way.  
```markdown
📚 Les in 1 zin
template:: lessen
soort:: [[📚lessen]]
onderwerp:: 
updates:: <%today%>
```

Very similar to the exploring notes as you can see. Instead of a title I use "les in 1 zin", which is lesson in one sentence. I removed stage as that is not necessary, but kept updates just in case. I added a new type, "lessen" (Dutch for lessons) to differentiate them.

As I write this I have now 9 exploration notes. So far using the template for them has really worked for me. I worked on my note about this set up for 4 days. June 15, 16, 18 and 19. I like that I can see that so easily now.

All I need to do now is get through the 14 open reference notes. These include 2 new ones added after this new set up. (for those keeping count, that means I have 10 closed references notes)

## Reference notes
For reference notes I use a pretty similar template. It just lacks the update properties. Instead I will link to a page where I actually work on processing the reference. I added the property "bron" (source) to link to the reference. The "soort" here is "uitzoeken" (investigate).

```markdown
`[[Referentie pagina]]`
template:: ref-link
soort:: [[🔍uitzoeken]]
onderwerp::
stadium:: 🌱🌿🌳
bron::
```

This part is still in prototype. I haven't used it all that much, so it isn't finished yet.  
So I will leave this for a future blogpost.

And that's it. That's all I'm doing right now in terms of "actual" Personal Knowledge Management.

[^1]: [Obligatory "I Took the Building a Second Brain Course" Post | jay l. colbert](https://wilde-at-heart.garden/pages/obligatory-i-took-the-building-a-second-brain-course-post/)