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
In the [previous post](../Bullet-Journaling-in-Logseq) I talked about how I implemented the idea of yearly, monthly and weekly pages in Logseq, today I want to dive into how I set up my daily log.  
How to organize and display my day has been a long struggle for me. I need the display to be structured and clear, so I don't get overwhelmed. Only this year have I found something that really seems to work for me.
## Daily template
I have a fixed part in my daily log. I use a Logseq template for this and use the config file to automatically pull it into the journal page of today, see below.

The config file entry.
```clojure
 ;; When creating the new journal page, the app will use your template if there is one.
 ;; You only need to input your template name here.
 :default-templates
 {:journals "dag"}
```

And the actual template.
![dagtemplate](dagtemplate.png)

After the properties for defining the template and that I don't want to include the bullet with the `template::` property, I have some page properties. A month property (maand) that I use for counting some activities and properties for tracking energy and stress. The energy and stress statistics I showed as part of my weekly log in the [previous post](../Bullet-Journaling-in-Logseq) originate from here.  
Below the properties there is a bullet for my day summary (📓dagboek/verwerken). I have a Braindump area which I also use for reflection. And finally a food intake section (voedsel/inname).  
I will go over each in more detail below. Except food intake, it's just a simple list of what I ate.
### Month property and counting activities
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
; Each line uses the output of the one below it, so read the details from bottom to top
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
### The summary of my day
I have a namespace `📓dagboek` and the subpage `📓dagboek/verwerken` that I use for writing a snippet about my day. These snippets get used for my week review to distil my week highlights.  
I also want to transfer them to my physical multi-year journal, so they have to be small. When transferred the subpage gets removed from the reference so I'll know how much I've left to process.
### Braindump area
Everything and anything that doesn't fit somewhere else goes here!  
I have a simple template for the area. First any symptoms that I experienced the entire day and below that something I'm proud of and something I'm grateful for.  
Beneath that, well, anything goes! Tasks that come to mind during the day, thoughts I have, reflections I do, etc. The only things that don't go here are what I did during the day, and that's what the next chapter is about.
## Day planning and execution
Every week, with my autism coach, I plan out the week ahead. What I will do in the morning, in the afternoon and in the evening. This forms the backbone of my daily log.

For this I have a week planning template which has a block for each day of the week. ==needs an updated screenshot==
![weekplanning](weekplanning.png)

My day is divided into 3 parts, morning (wake-up time till 12:30), afternoon (12:30 till 17:00) and evening (17:00 till going to bed). As every block functions basically the same way I'll focus on Monday afternoon. ==update screenshot==
![mondayafternoon](mondayafternoon.png)

