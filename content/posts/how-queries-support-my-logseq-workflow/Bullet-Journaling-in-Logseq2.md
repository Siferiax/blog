---
title: "Bullet Journaling in Logseq - Part 2"
date: 2023-06-22
series: 
  - How queries support my Logseq workflow
tags: 
  - Logseq
  - Bullet Journal
TocOpen: false
draft: false
---
In the [previous post](../bullet-journaling-in-logseq) I talked about how I implemented the idea of yearly, monthly and weekly pages in Logseq, today I want to dive into how I set up my daily log.  
How to organize and display my day has been a long struggle for me. I need the display to be structured and clear, so I don't get overwhelmed. Only this year have I found something that really seems to work for me.  
I settled with a daily log containing two main parts, a daily template covering daily statistics and an overview of my daily activities. Both have their dedicated chapter below.
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

The `template: dag` and `template-including-parent: false` properties are default Logseq properties to define this is a template. Indented under that is my custom template.  
Firstly, there is a month property (maand) that I use for counting some activities. Following that are six properties for tracking energy and stress for the day, o for morning, m for afternoon and a for evening (yes the letters are for Dutch words). The energy and stress statistics I showed as part of my weekly log in the [previous post](../bullet-journaling-in-logseq) originate from here.  
Below the properties there is a bullet for my day summary (📓dagboek/verwerken). I have a Braindump area which I also use for reflection. And finally a food intake section (voedsel/inname). This is just a simple list of what I ate.

Below, I will explain the month property, day summary and braindump area in more detail.
### Month property and counting activities
I'm going to use one of my main hobbies, gaming, as an example to show how I use this property. 

I have a page dedicated to games. This page contains a tracker counting how often I played games in a specific month. This is shown in a table, seen below (Aantal per maand).

This counting is done by, using a query, parsing the `#games` tag from a daily log entry (i.e. `[[Divinity Original Sin 2]] #games`), grouping based on the month property and counting the number of daily logs that contain this tag. The query is included below the table.

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
I have a namespace `📓dagboek` and its subpage `📓dagboek/verwerken` that I use for writing a snippet about my day. These snippets get used for my week review to distill my week highlights.  
Futhermore, I want to transfer these snippets to my physical multi-year journal. In order to keep track of those that I have processed I remove the subpage `/verwerken` (to process in Dutch) and hence move them to the `📓dagboek` namespace.
### Braindump area
Everything and anything that doesn't fit somewhere else goes here!  
I have a simple template for the area. First any symptoms (e.g. headache) that I experienced during the day and below something that I'm proud of and something that I'm grateful for.  
Beneath that, well, anything goes! Tasks that come to mind during the day, thoughts I have, reflections I do, etc.

This finalizes the daily template. The next chapter is about the daily activities part of the daily log.
## Daily activities
The daily activities are extracted from my weekly planning. Each week, with my autism coach, I plan out the week ahead. This details per day of the week what I will do in the morning, in the afternoon and what I will do in the evening.  
By using a specific date (using the `/date picker` command), Logseq automatically links the week plan to my daily log when it is generated. All I need to do then is move the plan from the linked reference section to my daily log.  
Below I show this in detail.

Every morning I go to the day's journal page and cut/paste the day plan from the week planning page (which is shown in the linked references of the journal page) into the journal page. 
![weekplanning_to_journal_1.png](weekplanning_to_journal_1.png)

After this move, the specific day sections are still children of the day link. This prevents the removal of the day link as it would also delete all its children. Hence, I outdent the blocks one level in order to place them as child on the day page. Then I can safely remove the day link. In the end it is as shown below.
![weekplanning_to_journal_2.png](weekplanning_to_journal_2.png)


As indicated before, each day has 3 parts, morning (wake-up time till 12:30), afternoon (12:30 till 17:00) and evening (17:00 till going to bed). Each has its own block and every block functions the same way. As an example, I'll focus on Monday afternoon. 
![mondayafternoon](mondayafternoon.png)

The first column is used to mark the activity as done. The second column provides a simple icon for the activity and the final column a description.
The icons in the first column on the fist and last row indicate my stress and energy levels at that point in time. I prefer to record these data points here to avoid having to scroll up to the top of the page to fill in the properties. Eventually, during the daily reflection I copy it into these properties.

The activities are intentionally such a small list, as it allows me to keep an overview and avoids me getting overwhelmed. The rows are, for the most part, things I have to get done. All extra time is not planned to give myself space and flexibility. 

During the day I will log what I do and what happens in the child bullets beneath the overview table. I don't log the items planned on the table, unless I have notes to add. As mentioned I check those off in the table itself.  
At the end of the day my Monday afternoon will then look something like this.
![mondayafternoon_finished.png](mondayafternoon_finished.png)
## Next time
While writing this and having to explain this all to my proof reader, I realized this process could be a lot simpler. So next time I will show you what my set up looks like now. 