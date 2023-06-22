---
title: "Bullet Journaling in Logseq - Part 1"
date: 2023-04-01
series: 
  - How queries support my Logseq workflow
tags: 
  - Logseq
  - Personal
TocOpen: false
draft: false
---
## History
In my previous post [The transition to Logseq](../the-transition-to-logseq) I mentioned I came to Logseq through Bullet Journaling (BuJo). This was at the end of 2022. I came across Bullet Journaling itself back in 2018. Every year I changed my notebook. Looking back over those notebooks, it was just experiment after experiment. Tweaking and adjusting to my needs in the moment. That's the beauty of BuJo, it is easy to adapt to your current needs.

I pared down my practice more and more. The main problem I ran into is that I never flipped back to previous pages. A nicely set up future log never got filled, the same happened to my monthly logs. All I really did was list my tasks for the day and ticked those off and wrote down what I spend my day doing.

Setting up pages became a hassle, I didn't have time in the morning, and in the evenings I didn't make the time for it.

And in 2022 I just stopped using my journal all together. I skipped a day in March... and another, and another. I tried picking it up again in April, but that didn't even last a week.

I needed a change.

I needed, to go digital! So I found Goodnotes. Still writing, but with the benefit of templates. No more hassle with setting things up. Perfect! And it was, until November when even Goodnotes started to get left behind. Something about the way I was journaling didn't work and I couldn't figure it out.

It wasn't that the [article](https://bulletjournal.com/blogs/bulletjournalist/how-i-use-digital-sidekicks-to-aid-my-bujojitsu) that led me to Logseq provided the answer, or even that Logseq itself did. At least not at first.

I was simply fascinated by Logseq and I decided to try it out.

Because I came to Logseq through Bullet Journaling, obviously that's what I tried to use it for.

Looking back at my November pages it is clear to see that I just started writing things down in the Logseq journal pages, time stamps and all. Slowly starting to use more and more features. But even this simple habit has changed a lot. My journal pages now look nothing like they did then. 
![daycomparison](daycomparison.png)

But for the past months, Logseq has been working for my BuJo practice. So today I want to take a deep dive into the way I now BuJo in Logseq. And to do this, I will start at the "shelf", the place you store notebooks, and end at the smallest level, the daily log.
## The Shelf
Because Logseq is "one thing" I had to invent some extra levels to the typical BuJo set up of 1+ notebooks a year with a future log, a monthly log, a weekly log and a daily log.

Instead of physical notebook boundaries or even digital folder boundaries, Logseq is a collection of free floating pages. So binding those pages together in some way is a consideration I had to make.

For the "shelf", the place to collect individual notebooks I decided to use the tag year overview (jaaroverzicht).

![jaaroverzicht](jaaroverzicht.jpg)

The concept of notebooks started as one notebook per year, even though many people end up needing more notebooks to fit their entire year in. For Logseq however there is no physical limit of notebook pages. Therefore I went with the one notebook per year analogy.
## The Notebook
The next level is the notebook itself, which will contain an entire year worth of pages. Date specific pages, as custom collections don't need to be captured in this same system. Something I like about using Logseq.

Each year will have its own page, the name of which simply being the year. (e.g. 2023)

On it I write down my yearly theme (as inspired by [CGP Grey](https://youtu.be/NVGuFdX5guE) on YouTube). This year it's Mindfulness. Both what it is and what it means for me.

I have a `Do in <year>` section, inspired by the [101 things list of JashiiCorrin](https://youtu.be/rAnFh2-SiWI). Which is new for 2023. So it's still an experiment.

And finally a monthly task overview, so I can see how well I did at a glance.

![jaarpagina](jaarpagina.jpg)

This page is probably the least interesting of the bunch. And also the page I come back to the least. About once a month to be exact.
## The Monthly Log
Next I have a page for each month of the year which uses the year as a namespace. So for example `2023/01 januari`. This means that a year has a hierarchy and the number is to sort the hierarchy in the correct order.

To set up my month pages I actually use a spreadsheet. I wish I could capture everything with templates, but I generate static dates and that's not supported (yet?). The main purpose of the spreadsheet has become the generation of my weekly logs which live on my monthly pages, more on them below.

![spreadsheet](spreadsheet.png)
![spreadsheet2](spreadsheet2.png)
![LegeMaandpagina](LegeMaandpagina.png)

As you can see, this saves a lot of manual labor. 

Other than weekly logs I fill my monthly page with my intention for the month and at the end of the month I add a monthly review (through the use of a template).

I use my intention as my guiding principle of what to engage with in the current month. Often my intention will contain tasks and I try to make my intentions as specific as possible. To keep my intentions at the forefront I add them to my startpage. More on that in the next blogpost.

At the end of the month the page will look something like this.

![maandpagina1](maandpagina1.jpg)
![maandpagina2](maandpagina2.jpg)
## The Weekly Log
My weekly logs (part of my month page) follows a simple format, at the start of the month each will have three sections.

![weeklylog](weeklylog.png)

The first section is the agenda.  
The purpose of the agenda is to give an overview of the activities of the week. I use an icon to indicated my workdays, such that I do not to make plans on those days. Weekly recurring activities are not in this overview. Instead they are recorded on a dedicated page and scheduled to repeat each week.

The "dagboek" section is my day summary, more on that below. "Statistieken" shows my energy and stress levels. These two sections are made up of queries to get data from my journal pages.

For my day summary, the query looks like this.
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
 :breadcrumb-show? false
 :collapsed? false
 :inputs [20230313 20230319]
}
#+END_QUERY
```
This results in an overview like below.
![dagboek](dagboek.png)

For the statistics I use the below query twice, I just select different property columns from the result.
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
Which looks like this
![statistieken](statistieken.png)
### Week review
At the end of the week, I will do a week review.  
I go over the week gone by. I assess how I did on my goals. Based on that I determine new goals for the week ahead.  
I note my week's highlights and determine what I'm proud of and what I'm thankful for. These then get added to my weekly log as shown below.

![weeklylogcomplete](weeklylogcomplete.png)
## The Daily Log
Most of the magic of my set-up happens in the daily log. However this blogpost is getting way too long, so I will save that magic for the [next post](../bullet-journaling-in-logseq2).