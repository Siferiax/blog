---
title: Using a start page
date: 2023-10-01
series:
  - How queries support my Logseq workflow
tags:
  - Logseq
TocOpen: false
draft: true
---
I wanted to make a blogpost about my new start page, however as I keep iterating on it, it kept changing. So every time I was working on a draft, it was quickly outdated.  
Still I keep trying. I'll go over the reason why I moved away from opening Logseq on the journal pages. The basic idea of the start page and how I keep changing things.  
Of course I will go over some of the queries I use and how to implement something similar yourself.

The previous blogposts have been building to this post. I've outlined my basic use of Logseq, but now it is time for queries.  
The biggest thing I ran into with the journal pages was that there was just too much on them. And a lot of this information isn't necessarily something I want to see all the time. On top of that there is information in pages other than my journal that I do want to see more easily.

This is how I got to my start page. One place to show what I need to see from whichever place. Therefore my start page consists solely of queries.  
In my very first blogpost I mentioned that I require a good overview. That's what my start page is suppose to help with. And that's what I've been constantly adjusting and improving.

At first my start page was incredibly long and had many different things on it. Not just things for today, but also for this week, this month, this year. And even though I mostly just used the top of the page, the rest was there, ready to overwhelm me.  

These days all the content is split between my start page and the default Contents page. The latter is shown by default when you open the sidebar, for quick access.  
My start page now only hold information that is relevant for me "at all times", whereas the Contents page is a collection of things I want quick access to. Some of the Contents page is redunant with my start page. This way I can still access it when working on another page in Logseq.

![startpage.png](startpage.png)

I mentioned in the [last post](../bullet-journaling-in-logseq2) of this series that I changed the daily log a ton. Well here you can see a glimpse of that. The first query, 🗓 Planning, shows my schedule. However this schedule no longer lives in my daily log. In fact the whole morning, afternoon, evening structure is missing. It is replaced with notes (📜 Notities) and activities (⏳ Activiteiten) also visible on the start page.

My planning for the week I put on a specific page called Weekplanning. Then I will show morning, afternoon or evening on the startpage. With the remaining blocks in the bottom query 🗓 Later vandaag (later today). The query checks whether all activities from a planning block have been crossed off (column below the ☑ has something in it). Here's the query with comments.

```clojure
#+BEGIN_QUERY
{:title [:h4 "🗓️ Planning"]
 :inputs [:today :right-now-ms :today-1030 :today-2000] ;input different times for reflection
 :query [:find (pull ?b [*])
  :in $ ?dag ?now ?o ?a ;variables of the inputs
  :where
   [?r :block/name "reflectie"] ;page with name reflectie
   [?w :block/name "weekplanning"] ;page with name weekplanning
   [?pb :block/page ?w] ;block that is on page weekplanning
   [?pb :block/refs ?p] ;this block references ?p
   [?p :block/journal-day ?dg] ;?p defined as the journal page with day ?dg
   [(<= ?dg ?dag)] ;?dg needs to be equal or smaller than today
   ;block that is returned
   (or 
     [?b :block/parent ?pb] ;or the block has the block on page weekplanning as parent
     [?b :block/page ?r] ;or the block is on page reflectie
   )
   [?b :block/content ?c] ;get the content for the block and set it to ?c
   ;block for the restriction of the dataset
   [?fb :block/parent ?pb] ;block has the block on page weekplanning as parent
   [?fb :block/content ?fc] ;get the content for the block and set it to ?fc
   (or
     (and ;before 10:30 show block with text "Begin van de dag" for morning reflection
       [(<= ?now ?o)] 
       [(clojure.string/includes? ?c "Begin van de dag")]
     )
     (and ;after 20:00 show block with text "Eind van de dag" for evening reflection
       [(<= ?a ?now)]
       [(clojure.string/includes? ?c "Eind van de dag")]
     )
     (and ;show morning plan
       [(clojure.string/includes? ?c "🌅 Ochtend")] ;?c includes this text
       [(clojure.string/includes? ?c "| |")] ;?c includes an empty table value
     )
     (and ;show afternoon plan
       [(clojure.string/includes? ?fc "🌅 Ochtend")] ;?fc includes this text
       (not [(clojure.string/includes? ?fc "| |")]) ;?fc doesn't include an table value
       [(clojure.string/includes? ?c "🌄 Middag")] ;?c includes this text
       [(clojure.string/includes? ?c "| |")] ;?c includes an table value
     )
     (and ;show evening plan
       [(clojure.string/includes? ?fc "🌄 Middag")] ;?fc includes this text
       (not [(clojure.string/includes? ?fc "| |")]) ;?fc doesn't include an table value
       [(clojure.string/includes? ?c "🌌 Avond")] ;?c includes this text
       [(clojure.string/includes? ?c "| |")] ;?c includes an table value
     )
   )
 ]
 :breadcrumb-show? false ;don't show the parent blocks
 :group-by-page? false ;don't group the results by page
}
#+END_QUERY
```

In simple terms, the morning block is shown when there are empty values in the morning table. The afternoon block is shown when the morning block doesn't have empty values and the afternoon one does. And the evening block is shown when the afternoon block doesn't have empty values and the evening one does.
The query Later vandaag is nearly the same, but it will show the afternoon block when it and the morning block have empty values. Same principle for the evening block, with it and the afternoon block having empty values.
This gives me a nice overview of my plans for the day, without overwhelming me.

The next two queries are for notes and activities.

IDEAS
- something on config file queries?


