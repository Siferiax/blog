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
Primarily I will go over the queries I use on the start page.

The previous blogposts have been building to this post. I've outlined my basic use of Logseq, but now it is time for queries.  
The biggest thing I ran into with the journal pages was that there was just too much on them. And a lot of this information isn't necessarily something I want to see all the time. On top of that there is information in pages other than my journal that I do want to see more easily.

This is how I got to my start page. One place to show what I need to see from whichever place. Therefore my start page consists solely of queries.  
In my very first blogpost I mentioned that I require a good overview. That's what my start page is suppose to help with. And that's what I've been constantly adjusting and improving.

At first my start page was incredibly long and had many different things on it. Not just things for today, but also for this week, this month, this year. And even though I mostly just used the top of the page, the rest was there, ready to overwhelm me.  

These days all the content is split between my start page and the default Contents page. The latter is shown by default when you open the sidebar, for quick access.  
My start page now only hold information that is relevant for me "at all times", whereas the Contents page is a collection of things I want quick access to. Some of the Contents page is redunant with my start page. This way I can still access it when working on another page in Logseq.

![startpage.png](startpage.png)

I mentioned in the [last post](../bullet-journaling-in-logseq2) of this series that I changed the daily log a ton. Well here you can see a glimpse of that. The first query, 🗓 Planning, shows my schedule. However this schedule no longer lives in my daily log. In fact the whole morning, afternoon, evening structure is missing. It is replaced with notes (📜 Notities) and activities (⏳ Activiteiten) also visible on the start page.
## Planning
My planning for the week I put on a specific page called Weekplanning. Then I will show morning, afternoon or evening on the start page. With the remaining blocks in the bottom query 🗓 Later vandaag (later today). The query checks whether all activities from a planning block have been crossed off (all cells in the table have content).  
It also has the second function of displaying morning and evening reflection based on time.

```clojure
#+BEGIN_QUERY
{:title [:h4 "🗓️ Planning"]
 :inputs [:today :right-now-ms :today-1030 :today-2000] ;input different times for reflection
 :query [:find (pull ?b [*])
  :in $ ?dag ?now ?o ?a ;variables of the inputs
  :where
   [?r :block/name "reflectie"] ;page with name reflectie
   [?w :block/name "weekplanning"] ;page with name weekplanning
   [?pb :block/page ?w] ;blocks that are on page weekplanning
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
       [(clojure.string/includes? ?c "| |")] ;?c includes an empty cell
     )
     (and ;show afternoon plan
       [(clojure.string/includes? ?fc "🌅 Ochtend")] ;?fc includes this text
       (not [(clojure.string/includes? ?fc "| |")]) ;?fc doesn't include an empty cell
       [(clojure.string/includes? ?c "🌄 Middag")] ;?c includes this text
       [(clojure.string/includes? ?c "| |")] ;?c includes an empty cell
     )
     (and ;show evening plan
       [(clojure.string/includes? ?fc "🌄 Middag")] ;?fc includes this text
       (not [(clojure.string/includes? ?fc "| |")]) ;?fc doesn't include an empty cell
       [(clojure.string/includes? ?c "🌌 Avond")] ;?c includes this text
       [(clojure.string/includes? ?c "| |")] ;?c includes an empty cell
     )
   )
 ]
 :breadcrumb-show? false ;don't show the parent blocks
 :group-by-page? false ;don't group the results by page
}
#+END_QUERY
```

In simple terms, the morning block is shown when there are empty cells in the morning table. The afternoon block is shown when the morning block doesn't have empty cells and the afternoon one does. And the evening block is shown when the afternoon block doesn't have empty cells and the evening one does.  
The query Later vandaag is nearly the same, but it will show the afternoon block when it and the morning block have empty cells. Same principle for the evening block, with it and the afternoon block having empty cells.  
This gives me a nice overview of my plans for the day, without overwhelming me.
## Notes and activities
The next two queries are for notes and activities.  
The notes query will always display the last block beneath the notes block. It is a space to quickly jot down thoughts and tasks as they come up. After writing them, I'll add a new bullet with enter so they are no longer displayed on the start page.  
The activities query will show all blocks beneath the activities block. They will all be displayed so I don't log an activity twice.

```clojure
#+BEGIN_QUERY
{:title [:h4 "📜 Notities"]
 :inputs [:today]
 :query [:find (pull ?b [*])
  :in $ ?dag
  :where
   [?p :block/journal-day ?dag] ;today's journal page
   [?par :block/page ?p] ;blocks on today's journal page
   [?par :block/content ?c] ;content for those blocks
   [(clojure.string/includes? ?c "📜 Notities")] ;content includes this text
   [?b :block/parent ?par] ;blocks that have the block in ?par as parent
   (not ;the following is excluded from the data set
     [?l :block/left ?b] ;?l is a sibling below ?b or a child of ?b
     (not [?l :block/parent ?b]) ;don't exclude ?b when ?l is a child.
   ) ;result: this clause limits ?b to only the last block for parent ?par.
 ]
 :breadcrumb-show? false
 :group-by-page? false
}
#+END_QUERY
```

Note: any variable as defined by `?` can have multiple matches/values. The number of those is reduced by the different clauses. So long as my journal page only has one block that contains the text "📜 Notities", `?par` will return only one specific block. Through the other clauses `?b` will end up being a specific block as well.

```clojure
#+BEGIN_QUERY
{:title [:h4 "⏳ Activiteiten"]
 :inputs [:today]
 :query [:find (pull ?b [*])
  :in $ ?dag
  :where
   [?p :block/journal-day ?dag] ;today's journal page
   [?par :block/page ?p] ;blocks on today's journal page
   [?par :block/content ?c] ;content for those blocks
   [(clojure.string/includes? ?c "Activiteiten")] ;content includes this text
   [?b :block/parent ?par] ;blocks that have the block in ?par as parent
 ]
 :breadcrumb-show? false
 :group-by-page? true
}
#+END_QUERY
```

The food and [redacted] query works similar to the activities query. Get specific blocks from today's journal page.
## Breaks and trackers
The breaks and trackers (🕯️ en ✴️) query works on properties. One annoying thing with properties is that for the simple query syntax `(property ?b :name "value")` the `"value"` part must be filled. When left to `""` it doesn't select those blocks with an empty value, instead it selects all blocks with that property regardless of value. This is why my query is not using that syntax throughout.  
This query will show different blocks based on different times and conditions.

```clojure
#+BEGIN_QUERY
{:title [:h4 "🕯️ en ✴️"]
 :inputs [:today :right-now-ms :today-1230 :today-1700] ;input different times to use
 :query [:find (pull ?b [*])
  :in $ ?dag ?now ?o ?m ;variables of the inputs
  :where
   [?p :block/journal-day ?dag] ;today's journal page
   [?b :block/page ?p] ;blocks on today's journal page
   [?b :block/content ?c] ;content for those blocks
   (or-join [?p ?b ?c ?now ?o ?m] ;or-join as not all variables can be unified
   ;to clarify for an or to work all clauses within need to use the same variables
   ;if they don't you will need to use an or-join and specify which variables are used in the entire query
     (property ?b :laxeermiddel "➖") ;always show property laxeermiddel when its value is ➖
     (and ;show property vitaminepil when its value is ➖ and it is currently at or past 17:00
       (property ?b :vitaminepil "➖") 
       [(<= ?m ?now)]
     )
     (and ;always show property 🌅🔋 when it isn't filled in
       [?b :block/properties ?prop]
       [(get ?prop :🌅🔋) ?v]
       [(= ?v "")]
     )
     (and ;show property 🌄🔋 when it isn't filled in and it is at or past 12:30
       [?b :block/properties ?prop]
       [(get ?prop :🌄🔋) ?v]
       [(= ?v "")]
       [(<= ?o ?now)]
     )
     (and ;show property 🌌🔋 when it isn't filled in and it is at or past 17:00
       [?b :block/properties ?prop]
       [(get ?prop :🌌🔋) ?v]
       [(= ?v "")]
       [(<= ?m ?now)]
     )
     [(= ?c "O: ➖")] ;show blocks which content is exactly equal to O: ➖
     (and ;show blocks with content M: ➖ when there are no blocks with content O: ➖
       [(= ?c "M: ➖")]
       (not
         [?e :block/page ?p]
         [?e :block/content ?entry]
         [(= ?entry "O: ➖")]
       )
     )
     (and ;show blocks with content A: ➖ when there are no blocks with content M: ➖
       [(= ?c "A: ➖")]
       (not
         [?e :block/page ?p]
         [?e :block/content ?entry]
         [(= ?entry "M: ➖")]
       )
     )
   )
 ]
 :breadcrumb-show? false
 :group-by-page? false
}
#+END_QUERY
```
## Tasks
The Taakjes (tasks) query is my task list for the day, with a small twist. It will only list those tasks that are either scheduled for today or unscheduled on today's journal page. And then only those tasks that have not been planned into my weekplanning. (I use markdown links `[]()` for that, see "Bridges & Twists" in the screenshot)  

```clojure
#+BEGIN_QUERY
{:title [:b "☑️ Taakjes"]
 :inputs [:today]
 :query [:find (pull ?b [*])
  :in $ ?day
  :where
   [?p :block/journal-day ?day] ;today's journal page
   [?b :block/marker "TODO"] ;tasks with marker TODO
   (or
     [?b :block/scheduled ?day] ;either the task is scheduled for today
     (and ;or the task lives on today's journal page and isn't scheduled
       [?b :block/page ?p]
       (not [?b :block/scheduled])
     )
   )
   (not ;exclude all tasks that are referenced on the weekplanning page
     [?w :block/name "weekplanning"]
     [?pb :block/page ?w] ;blocks on page weekplanning
     [?pb :block/refs ?j] ;that reference ?j
     [?j :block/journal-day ?d] ;where ?j is a journal page of date ?d
     [(>= ?d ?day)] ;?d is equal or greater than today
     [?plan :block/parent ?pb] ;blocks whose parent is ?pb
     [?plan :block/refs ?b] ;block references task ?b
   ) ;this specifically ensures that tasks planned on previous days do show
 ]
 :result-transform :sort-by-list ;result-transform specified in the config file
 :breadcrumb-show? false
}
#+END_QUERY
```

I have a lot of queries that pull out specific tasks. I generally sort my tasks the same way each time. So I have specified the result-transform in the config file with name `:sort-by-list` for quick access and easy maintenance. 
## Focus this week
The last query to show is Focus deze week (focus this week). Each week I do a week review and set goals and a focus for the coming week. The goals (tasks) are only shown in my contents page, but my focus I want to have as a constant reminder on my startpage.  
All those blocks are under the same parent and I simply exclude the task blocks in this query.

```clojure
#+BEGIN_QUERY
{:title [:h4 "🎯 Focus deze week"]
 :inputs [:today]
 :query [:find (pull ?doel [*])
  :in $ ?today
  :where
   [(str ?today) ?dag] ;convert the input to a string (is an integer)
   [(subs ?dag 0 4) ?jaar] ;get the first 4 character (that's the year)
   [?ns :block/name ?jaar] ;get the page with the name of this year
   [?p :block/namespace ?ns] ;get the pages within the namespace (the months)
   [?j :block/journal-day ?today] ;get today's journal page
   [?agenda :block/refs ?j] ;get the blocks that reference today's journal page
   [?agenda :block/page ?p] ;limit those blocks to only the one on a month page
   [?agenda :block/parent ?week] ;the parent is the block for the week
   [?header :block/parent ?week] ;?header is another block under the same week block
   [?header :block/content ?hc] ;get the content for ?header
   [(clojure.string/includes? ?hc "🎯 Week Doelen")] ;content includes this text
   [?doel :block/parent ?header] ;get the blocks under ?header
   (not [?doel :block/marker]) ;exclude the blocks that are tasks
 ]
 :group-by-page? false
 :breadcrumb-show? false
}
#+END_QUERY
```

Alternatively I could get the month page that belongs to today directly, however my month pages are based on full weeks. So for example November 1st 2023 is actually on the October page and not the November page. (the first day of a week determines the page I put it on)


The "Meer..." (more) link at the bottom is a simple markdown link to the contents page. I mainly use this on mobile which doesn't have a sidebar in portrait mode.

And that's my start page. Giving me a solid overview I can look at throughout the day.
I'll cover the contents page in another blogpost.