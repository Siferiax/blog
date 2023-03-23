---
title: "Bullet Journaling in Logseq - Part 1"
date: 2023-03-15
series: 
  - How queries support my Logseq workflow
tags: 
  - Logseq
  - Personal
TocOpen: false
draft: true
---
## History
In my previous post [The transition to Logseq](../the-transition-to-logseq) I mentioned I came to Logseq through Bullet Journaling (BuJo). This was at the end of 2022. I came across Bullet Journaling itself back in 2018. Every year I changed my notebook. Looking back over those notebooks, it was just experiment after experiment. Tweaking and adjusting to my needs in the moment. That's the beauty of BuJo, it is easy to adapt to your current needs.

I paired down my practice more and more. The main problem I ran into is that I never flipped back to previous pages. A nicely set up future log never got filled, the same happened to my monthly logs. All I really did was list my tasks for the day and ticked those off and wrote down what I spend my day doing.

Setting up pages became a hassle, I didn't have time in the morning, and in the evenings I didn't make the time for it.

And in 2022 I just stopped using my journal all together. I skipped a day in March... and another, and another. I tried picking it up again in April, but that didn't even last a week.

I needed a change.

I needed, to go digital! So I found Goodnotes. Still writing, but with the benefit of templates. No more hassle with setting things up. Perfect! And it was, until November when even Goodnotes started to get left behind. Something about the way I was journaling didn't work and I couldn't figure it out.

It wasn't that the [article](https://bulletjournal.com/blogs/bulletjournalist/how-i-use-digital-sidekicks-to-aid-my-bujojitsu) that led me to Logseq provided the answer, or even that Logseq itself did. At least not at first.

I was simply fascinated by Logseq and I decided to try it out.

Because I came to Logseq through Bullet Journaling, obviously that's what I tried to use it for.

Looking back at my November pages it is clear to see that I just started writing things down in the Logseq journal pages, time stamps and all. Slowly starting to use more and more features. But even this simple habit has changed a lot. My journal pages now look nothing like they did then.

But for the past months, Logseq has been working for my BuJo practice. So today I want to take a deep dive into the way I now BuJo in Logseq. And to do this, I will start at the "shelf", the place you store notebooks, and end at the smallest level, the daily log.
## The Shelf
Because Logseq is "one thing" I had to invent some extra levels to the typical BuJo set up of 1+ notebooks a year with a future log, a monthly log, a weekly log and a daily log (and of course custom collections).

Instead of physical notebook boundaries or even digital folder boundaries, Logseq is a collection of free floating pages. So binding those pages together in some way is a consideration I had to make.

For the "shelf", the place to collect individual notebooks I decided to use the tag year overview (jaaroverzicht).

![jaaroverzicht](jaaroverzicht.jpg)

The concept of notebooks started as one notebook per year, even though many people end up needing more notebooks to fit their entire year in. For Logseq however there is no physical limit of notebook pages. Therefore I went with the one notebook per year analogy.
## The Notebook
The next level is the notebook itself, which will contain an entire year worth of pages. Date specific pages, as custom collections don't need to be captured in this same system. Something I like about using Logseq.

Each year will have its own page, the name of which simply being the year. (e.g. 2023)

On it I write down my yearly theme (as inspired by [CGP Grey](https://youtu.be/NVGuFdX5guE) on YouTube). Both what it is and what it means for me.

I have a `Do in <year>` section, inspired by the [101 things list of JashiiCorrin](https://youtu.be/rAnFh2-SiWI). Which is new for 2023. So it's still an experiment.

And finally a monthly task overview, so I can see how well I did at a glance.

![jaarpagina](jaarpagina.jpg)

This page is probably the least interesting of the bunch. And also the page I come back to the least. About once a month to be exact.
## The Monthly Log
Next I have a page for each month of the year which uses the year as a namespace. So for example `2023/01 januari`. This means that a year has a hierarchy and the number is to sort the hierarchy in the correct order.

To set up my month pages I actually use a spreadsheet. I wish I could capture everything with templates, but I generate static dates and that's not supported (yet?). The main purpose of the spreadsheet has become the generation of my weekly logs which live on my monthly pages, more on them below.

![spreadsheet.png](spreadsheet.png)
![spreadsheet2.png](spreadsheet2.png)
![LegeMaandpagina.png](LegeMaandpagina.png)

As you can see, this saves a lot of manual labor.

Other than weekly logs I fill my monthly page with my intention for the month and at the end of the month I add a monthly review (through the use of a template).

I use my intention as my guiding principle of what to engage with in the current month. Often my intention will contain tasks and I try to make my intentions as specific as possible. To keep my intentions at the forefront I add them to my startpage. More on that in the next blogpost.

At the end of the month the page will look something like this.

![maandpagina1](maandpagina1.jpg)
![maandpagina2](maandpagina2.jpg)
## The Weekly Log
My weekly logs follow a simple format, at the start of the month each will have an agenda and two (collapsed) sections.

![weeklylog.png](weeklylog.png)

The agenda is there to give an overview of the "special" happenings of the week. These can be appointments, tasks or intentions. Tasks that happen each week again and again are not recorded here.

What is recorded are my workdays using an icon behind the date. To prevent myself from making plans on those days.

The collapsed sections are my dairy (or more accurately my day summary, more on that below.) and statistics about my energy and stress levels. These two sections are made up of queries to get data from my journal pages.

For my dairy, the query looks like this.
```clojure
#+BEGIN_QUERY
{:query [:find (pull ?b [*])
   :in $ ?start ?end
   :where
     [?r :block/name "📓dagboek"]
     [?b :block/refs ?r]
     [?b :block/page ?p]
     [?p :block/journal-day ?d]
     [(>= ?d ?start)]
     [(<= ?d ?end)]
 ]
 :table-view? false
 :collapsed? false
 :inputs [20230313 20230319]
}
#+END_QUERY
```

For the statistics I use the same query twice, I just select different property columns from the result.
```clojure
#+BEGIN_QUERY
{:title "Energie"
 :query [:find (pull ?p [*])
   :in $ ?start ?end
   :where
     [?p :block/journal-day ?d]
     [(>= ?d ?start)]
     [(<= ?d ?end)]
     [?p :block/file _]
 ]
 :result-transform (fn [result] (sort-by (fn [s] (get-in s [:block/journal-day])) result))
 :table-view? true
 :collapsed? false
 :inputs [20230313 20230319]
}
#+END_QUERY
```

At the end of the week, I will do a week review.  
I go over the week gone by and assess how I did on my goals, what my week's highlights were, what I'm proud of and what I'm thankful for. I also determine my new goals for the week ahead.  
Those highlights, what I'm proud of and thankful for then get added to my weekly log.

![weeklylogcomplete.png](weeklylogcomplete.png)
## The Daily Log
Most of the magic of my set-up happens in the daily log. How to display my day has been a long struggle for me and only this year have I found something that really seems to work for me.

There are a few separate components to my daily log. A big important part of it I make every week with my autism coach. We plan my entire week in a big picture sense. What will I do in the morning, what in the afternoon and what in the evening.

I'll get into the details in just a second, but first I want to address the smaller recurring daily part of the daily log. I have a template for this and use the config file to automatically pull it into the journal page of today.

```clojure
 ;; When creating the new journal page, the app will use your template if there is one.
 ;; You only need to input your template name here.
 :default-templates
 {:journals "dag"}
```

Here's what it looks like.
![dagtemplate.png](dagtemplate.png)

I have a number of page properties for tracking energy and stress, these are what show up in the statistics part of my weekly log mentioned earlier.  
I also have a month property (maand) that I use for counting some activities.  
There's a bullet for my dairy (📓dagboek/verwerken) and a Braindump part which I use both as reflection and well, braindump area. And finally a food intake section (voedsel/inname)
### Monthly property and counting activities
I'm going to use one of my main hobbies, gaming, as an example to show how I use this property. I have a page dedicated to games. Every game itself has a page, but this page contains the overview of everything game related.

This overview includes trackers for when I last played a game, regardless of which one specifically. In my daily log I will track something like `[[Divinity Original Sin 2]] #games` (you may have seen this one in the previous screenshots 😉)  
The `#games` is the part that I use on this page to get the info I need. The second bit is the month property. I have a table list of how often I played a game in the last 12 months.

![gamepermaand.png](gamepermaand.png)

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
### My dairy in more detail
I have a namespace `📓dagboek` and the subpage `📓dagboek/verwerken` that I use for writing a snippet about my day. These snippets get used for my week review to distil my week highlights.  
I also want to transfer them to my physical multi-year journal, so they have to be small. When transferred the subpage gets removed from the reference so I'll know how much I've left to process.
### Braindump area
Everything and anything that doesn't fit somewhere else goes here!  
I have a simple template for the area. First any symptoms that I experienced the entire day and below that something I'm proud of and something I'm grateful for.  
Beneath that, well, anything goes! Tasks that come to mind during the day, thoughts I have, reflections I do, etc. The only things that don't go here are what I did during the day. We'll get to planning and execution in a bit.
### Food intake
Lastly I record when and what stuff I put in my mouth. Simple as that.

I hope you enjoyed this first view into how I put Bullet Journal concepts to work in my use of Logseq. Next post I will explore my daily log further and go into how I do my weekly daily planning.