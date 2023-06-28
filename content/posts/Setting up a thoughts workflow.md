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
I've been exploring [Zettelkasten](https://zettelkasten.de/) a lot. Reading tons of articles for inspiration and understanding. Every time I concluded it didn't fit me. This was not *my* PKM system.
At the same time I was very frustrated about my set up.

However recently I stumbled upon [Jay Colbert's Zettelkasten workflow](https://wilde-at-heart.garden/pages/my-zettelkasten-workflow-from-start-to-finish/).
What I like about Jay's workflow is that he starts from the journal page for everything. And that he has a specific template for each of his note types.

At this time I have gathered 42 notes I would consider part of my Personal Knowledge database. I feel this is a nice amount to experiment with. It provides enough bulk to see if and how a set up works. And there aren't too many notes to put in a different set up.

The humble fleeting note. I first decided, based on my understanding, that this was my ideas. Thoughts I hadn't actually processed further.
I still like this idea. The notion that "everything is a fleeting note", doesn't quite work well for me. The note that states "today I played World of Warcraft" isn't really a thought that needs further processing. That's just what I did that day.
Jay makes this distinction as well. Using a template for those fleeting notes to be processed more in the future.

I've copied much of his workflow on a process level. The exact property names differ, but they are very similar. However my definitions and how I actually use the system are different.

I completely drop the notion of atomic notes, or zettels. 
Examples I've seen don't seem to map on my own notes in terms of structure.
I just don't like the idea of needing 10 different zettels to express one (complex) idea.

The main drawback of Zettelkasten for me is two fold I think. The "unique" and "atomic" idea writing and the idea of making an individual page for all of that.

I just never get to neatly defined thoughts to put in a zettelkasten.
I'm not ~~a writer anyway~~ that kind of writer anyway. ("Zettelkasten is a tool for writing" - [Bob Doto](https://writing.bobdoto.computer))

I don't want to dismiss zettelkasten outright though. I want to keep the possibility open.
I copied all the templates from Jay's workflow and put the 42 notes I had in the templates I felt they fit in.
While doing so I molded the templates to something fit for me.
==soorten uitleggen==

==Focus op fleeting==

Jay uses fleeting, and I decided soort would be `🧭verkenning` (I like icons!), translated from exploring. 

Working through this, I've landed on the term best suited for what I am actually doing: exploring. I am exploring my thoughts as I write them down.

For each of Jay's templates I considered each of the properties. Which to keep or change and which I didn't need. I also tried to translate them to Dutch in my continued effort to keep my graph in my native language as much as possible.

Date => Updates
In Jay's workflow the property `date` is used. As all notes live on a journal page this doesn't add value for me. Jay cites it's about sorting. Using advanced queries I get the journal-day attribute which is in `YYYYMMDD` format for sorting.
What I do want is the ability to refine my note. So I don't want to use references and embeds, I want to change the note itself.
And I want to see this somehow.
I decided on the property name `updates`. In updates I will put all journal page links for dates that I worked on the note. I feel the Dutch word "aanpassingen" is too long, so I kept this one English.

Tags => Onderwerp
I use `onderwerp` instead of `tags`. I use tags for pages as they are an actual attribute in queries to use. And I specifically use tags for the P.A.R.A set up, but that will be a separate blogpost.
Onderwerp translates to subject and that is exactly how I use it.

Type => Soort
I changed `type` to `soort`. It's a synonym as type is also a Dutch word, but I like soort better in this case. 

Status => Stadium
I like the growth analogy from Digital Gardens. Status doesn't really work well with that. Besides I use that property for other things already. So I choose `stadium`, Dutch for stage.
Jay uses status for his zettels. I like to use them for my version of fleeting notes as well.
It also serves as a replacement for the `TODO` Jay uses. I filter my notes based on `stadium` instead.

This is the final template:
```markdown
🧭 Notitie titel
template:: verkenning
soort:: [[🧭verkenning]]
onderwerp:: 
stadium:: 🌱🌿🌳
updates:: <%today%>
```

==Focus op referenties==


References
```markdown
LATER `[[Referentie link]]`
template:: ref-link
soort:: [[🔍uitzoeken]]
onderwerp::
```

==Focus op potentiële zettels==

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
	- > quote etc.[1]  
	- [^1]: bron van quote etc. Ref link
	- ---
	- 🔼 bovenliggend inzicht
	- ---
	- 🔁 relaties met andere inzichten
```

==Bonus: lessen==

I have added the soort `📚lessen` (lesson), as I felt the need to log those as a separate category. 
```markdown
📚 Les in 1 zin
template:: lessen
soort:: [[📚lessen]]
onderwerp:: 
updates:: <%today%>
```
