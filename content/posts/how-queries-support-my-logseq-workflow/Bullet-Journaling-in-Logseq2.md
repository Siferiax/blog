---
title: "Bullet Journaling in Logseq - Part 2"
date: 2023-04-15
series: 
  - How queries support my Logseq workflow
tags: 
  - Logseq
  - Personal
TocOpen: false
draft: true
---
How to display my day has been a long struggle for me and only this year I have found something that really seems to work for me.

I will go over the componen......==to continue prepare reader for the shit that is coming.==

Every week, with my autism coach, I plan out the week ahead. What will I do in the morning, what in the afternoon and what in the evening. This forms the backbone of my daily log.

I have a fixed part in my daily log.

I'll get into the details of that in the next post, for now I want to address the smaller recurring part of the daily log. I use a Logseq template for this and use the config file to automatically pull it into the journal page of today see below.

```clojure
 ;; When creating the new journal page, the app will use your template if there is one.
 ;; You only need to input your template name here.
 :default-templates
 {:journals "dag"}
```

Here's what it looks like.
![dagtemplate](dagtemplate.png)

I have a month property (maand) that I use for counting some activities.  
I have a number of page properties for tracking energy and stress, these are what show up in the statistics part of my weekly log mentioned earlier.  
There's a bullet for my diary (📓dagboek/verwerken) and a Braindump part which I use both as reflection and well, braindump area. And finally a food intake section (voedsel/inname)
### Monthly property and counting activities
I'm going to use one of my main hobbies, gaming, as an example to show how I use this property. I have a page dedicated to games. Every game itself has a page, but this page contains the overview of everything game related.

This overview includes trackers for when I last played a game, regardless of which one specifically. In my daily log I will track something like `[[Divinity Original Sin 2]] #games` (you may have seen this one in the previous screenshots 😉)  
The `#games` is the part that I use on this page to get the info I need. The second bit is the month property. I have a table list of how often I played a game in the last 12 months.

![gamepermaand](gamepermaand.png)

And the underlying query. Including comments explaining the whole thing!
```clojure
#+BEGIN_QUERY 
{:title [:h3 "Aantal per maand"]
; the query result will give all attributes of variable ?p
 :query [:find (pull ?p [*]) 
   :where
; get the id of the games page. (:block/name is always lowercase)
     [?r :block/name "games"] 
; get the blocks which reference this id. So through [[games]] or #games
     [?b :block/refs ?r] 
; get the page that the block is on.
     [?b :block/page ?p] 
; make sure the page is a journal page.
     [?p :block/journal? true] 
 ]
; Summary: The pages are grouped together and counted for each value of :maand
; then it sorts the result and takes the top 12 to display.
; Each line use the output of the one below it, so read the details from bottom to top
 :result-transform (fn [result] 
; Sort the map by :month in reverse (the > symbol)
; and take the top 12 results (in this case page count by month)
   (take 12 (sort-by :month > 
; Count the number of pages with the same value for :maand and put them in a key value pair.
     (map (fn [[key value]] {:month key :count (count value)}) 
; Take the query result and group it by the value of :maand from the properties attribute.
       (group-by (fn [g] (get-in g [:block/properties :maand])) result) 
     )
   ))
 )
 ; The result from all the above is displayed in a table as defined below.
 ; This uses hiccup (Hiccup is a library for representing HTML in Clojure)
 :view (fn [rows] [:table [:thead [:tr [:th "Maand"] [:th "Aantal"] ]] 
 [:tbody (for [r rows] [:tr [:td (get r :month)] [:td (get r :count)] ])] ])
}
#+END_QUERY
```
### My diary in more detail
I have a namespace `📓dagboek` and the subpage `📓dagboek/verwerken` that I use for writing a snippet about my day. These snippets get used for my week review to distil my week highlights.  
I also want to transfer them to my physical multi-year journal, so they have to be small. When transferred the subpage gets removed from the reference so I'll know how much I've left to process.
### Braindump area
Everything and anything that doesn't fit somewhere else goes here!  
I have a simple template for the area. First any symptoms that I experienced the entire day and below that something I'm proud of and something I'm grateful for.  
Beneath that, well, anything goes! Tasks that come to mind during the day, thoughts I have, reflections I do, etc. The only things that don't go here are what I did during the day. We'll get to planning and execution in a bit.
### Food intake
Lastly I record when and what stuff I put in my mouth. Simple as that.

I hope you enjoyed this first view into how I put Bullet Journal concepts to work in my use of Logseq. Next post I will explore my daily log further and go into how I do my weekly daily planning.


Today I will dive further into my daily log and how I plan my days in Logseq. As mentioned in my previous post I plan my days together with my coach each week.

For this I have a week planning template which has a block for each day of the week.
![weekplanning](weekplanning.png)

My day is divided into 3 parts, morning (wake-up time till 12:30), afternoon (12:30 till 17:00) and evening (17:00 till going to bed). As every block functions basically the same way I'll focus on Monday afternoon.
![mondayafternoon](mondayafternoon.png)

 (and of course custom collections).  
"Collection Modules, or Collections, are great for organizing specific lists (shopping list, reading lists etc), classes, and projects." -- [bulletjournal.com](https://bulletjournal.com/blogs/bulletjournalist/collections)