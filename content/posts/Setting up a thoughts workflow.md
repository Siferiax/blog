---
title: "Thoughts Workflow"
date: 2023-06-23
series: 
  - Musings
tags:
  - Logseq
  - Personal
  - PKM
TocOpen: false
draft: true
---
I've been exploring [Zettelkasten](https://zettelkasten.de/) a lot. Reading tons of articles for inspiration and understanding. And recently I stumbled upon [Jay Colbert's Zettelkasten workflow](https://wilde-at-heart.garden/pages/my-zettelkasten-workflow-from-start-to-finish/). Inspiring change and this blogpost.

I have noticed an increase in notes I need to dig up and work on more at a later time.  
And even though zettelkasten isn't the way forward, Jay's article gave me the ideas required to find a workflow for myself.

In this blogpost I will show you the simple system I made for myself based on Jay's article. Reading his article will give extra context, but I will do my best to not make it required reading for this one.

## Zettelkasten vs. my notes
The zettelkasten system as I have come to understand it has three categories of notes: fleeting notes, zettels (a.k.a. main notes, or sometimes permanent notes) and reference notes (a.k.a. literature notes).

A lot of my notes would fall in the category of reference notes. I love collecting information.
Otherwise my notes are in the space between fleeting and zettels.

I interpret fleeting notes as short term, small notes to work on later. Whereas main notes are self contained ideas (a.k.a. atomic). As I understand zettelkasten, one zettel is one single idea to keep in your system permanently.  
My notes are often multiple related ideas. Or one big, complex idea. They may actually be "done" in contrast with fleeting notes. Which means, they are fleshed out and have reached a conclusion.

What I like about Jay's workflow is that he starts from the Logseq journal page for everything. And that he has a specific template for each of his note types.

At this time I have gathered 42 notes I would consider part of my Personal Knowledge database. I feel this is a nice amount to experiment with. It provides enough bulk to see if and how a set up works. And there aren't too many notes to put in a different set up.

## Not actually zettelkasten
I completely drop the notion of atomic notes, or zettels. 
Examples I've seen don't seem to map on my own notes in terms of structure.
I just don't like the idea of needing 10 different zettels to express one (complex) idea.
I just never get to neatly defined thoughts to put in a zettelkasten.
I'm not ~~a writer anyway~~ that kind of writer anyway. ("Zettelkasten is a tool for writing" - [Bob Doto](https://writing.bobdoto.computer))

## Fleeting notes
I first decided, based on my understanding, that fleeting notes were my ideas. Thoughts I hadn't actually processed further.
I still like this idea. The notion that "everything is a fleeting note", doesn't quite work well for me. The note that states "today I played World of Warcraft" isn't really a thought that needs further processing. That's just what I did that day.
Jay makes this distinction as well. Using a template for those fleeting notes to be processed more in the future.
He writes,
> In Logseq, I have a template for the fleeting notes I know will feed into my Zettelkasten. Usually, these are ideas for Zettels.

Fleeting is just not the right term for me. My notes are in the space between fleeting (I read that as short term) and zettels (long term, keep forever, single idea notes).
In addition I make an effort to keep my graph in my native language (Dutch) as much as possible.

On his website Jay explains that he uses his Digital Garden for divergence and his Zettelkasten for convergence.[^1]
My notes are often both. First I brainstorm and they diverge, then I refine the note to be more concise and to the point. This is what I did to contemplate my new set up. Brainstorming what my notes were for, what structure they followed. 
This helped me find the term best suited for what I am actually doing: exploring. I am exploring my thoughts as I write them down.
Translating that I got to the note type `🧭verkenning` (I like icons!).

For the template, I rearranged and changed Jay's fleeting note template.

```
- TODO **Note goes here**
  date::
  tags:: 
  type:: [[fleeting]]
```

```
- 🧭 Notitie titel
  template:: verkenning
  soort:: [[🧭verkenning]]
  onderwerp:: 
  stadium:: 🌱🌿🌳
  updates:: <%today%>
```

I changed `type` to `soort`. It's a synonym as type is also a Dutch word, but I like soort better in this case. 

I use `onderwerp` instead of `tags`. Onderwerp is the Dutch word for subject. I choose this to indicate the purpose of the property more clearly.

I like the growth analogy from Digital Gardens, to indicate the maturity of my exploration. So I added the property `stadium`, Dutch for stage.
It also serves as a replacement for the `TODO` Jay uses. I already use `TODO` for my task management.

In Jay's workflow the property `date` is used. As all notes live on a journal page this doesn't add value for me. Jay cites it's about sorting. Using advanced queries I get the journal-day attribute which is in `YYYYMMDD` format for sorting.
What I do want is the ability to refine my note. So I don't want to use references and embeds, I want to change the note itself. And I want to see that I changed it.
I decided on the property `updates`. In updates I will put all journal page links for dates that I worked on the note. I feel the Dutch word "aanpassingen" is too long, so I kept this one English.

Finally, I put a title at the top and then use indented bullets underneath this template to further explore my thoughts.

## Reference notes
References
```markdown
LATER `[[Referentie link]]`
template:: ref-link
soort:: [[🔍uitzoeken]]
onderwerp::
```

## Zettels
I don't want to dismiss zettelkasten outright though. I want to keep the possibility open.
I copied all the templates from Jay's workflow and put the 42 notes I had in the templates I felt they fit in.
While doing so I molded the templates to something fit for me.

==Insight uitleggen==
I also adjusted the zettel templates from Jay. The page template is nearly the same.
I use a query in the journal template to get some properties from the page so I don't need to record them twice.
I haven't actually tested these templates for my own workflow, so I don't know how well they work for me.

```markdown
LATER [[🧿inzicht]] `[[Notitie titel]]`
template:: inzicht
	- query-properties:: [:onderwerp :stadium :updates]
	  #+BEGIN_QUERY
	  {:title [:b "Eigenschappen"]
 	   :query [:find (pull ?p [*])
 	    :in $ ?parent
  	    :where
   	     [?parent :block/refs ?p]
         [?p :block/name ?n]
         (not [(contains? #{"later" "🧿inzicht"} ?n)])
       ]
       :inputs [:parent-block]
	  }
	  #+END_QUERY
```

```markdown
template:: inzicht-pagina
template-including-parent:: false
	- soort:: [[🧿inzicht]]
	  onderwerp::
	  stadium:: 🌱🌿🌳
	  updates:: <%today%>
	- Inzicht verder uitgediept
	- > quote etc.[^1]  
	- [^1]: bron van quote etc. Ref link
	- ---
	- 🔼 bovenliggend inzicht
	- ---
	- 🔁 relaties met andere inzichten
```

## Bonus: lessons
I have added the soort `📚lessen` (lesson), as I felt the need to log those as a separate category. 
```markdown
📚 Les in 1 zin
template:: lessen
soort:: [[📚lessen]]
onderwerp:: 
updates:: <%today%>
```

[^1]: insert link