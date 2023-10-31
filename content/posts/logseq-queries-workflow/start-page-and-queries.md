---
title: My start page and its queries
date: 2023-10-30
series:
  - How queries support my Logseq workflow
tags:
  - Logseq
TocOpen: false
draft: false
---
I wanted to make a blogpost about my new start page, however as I keep iterating on it, it kept changing. So every time I was working on a draft, it was quickly outdated.  
Still, I'll keep trying. I'll go over the reason why I moved away from opening Logseq on the journal pages (default setting), the basic idea of the start page and how I keep changing things.  
Primarily I will go over the queries I use on the start page.

The previous blogposts have been building to this post. I've outlined my basic use of Logseq, but now it is time for queries.  
The biggest thing I ran into with the journal pages was that there was just too much on them. And a lot of this information isn't necessarily something I want to see all the time. On top of that, there is information in pages other than my journal, that I do want to see more easily.

This is how I got to my start page. One place to show all the information I need. Therefore, my start page consists solely of queries.  
In my very first blogpost I mentioned that I require a good overview. That's what my start page is supposed to help with. And that's what I've been constantly adjusting and improving as I use it.

At first, my start page was incredibly long and had many different things on it. Not just things for today, but also for this week, this month, this year. And even though I mostly just used the top of the page, the rest was there, ready to overwhelm me.  

These days, all the content is split between my start page and the default Contents page. The latter is shown by default when you open the right sidebar.  
My start page now only holds information that is relevant for me "at all times", whereas the Contents page is a collection of things I want quick access to.
I'll cover the contents page in another blogpost.

Let's talk about the start page in detail. Below you'll see a screenshot of the entire page. I will tackle it one query at a time, starting with 🗓 Planning.

![startpage.png](startpage.png)

🗓 Planning, shows my schedule for today. The query pulls this information from a page called Weekplanning where I plan my entire week.
I show morning, afternoon or evening of today on the start page.

Here's the query I use to accomplish this.
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

In simple terms, the morning block is shown when there are empty cells in the morning table. The afternoon block is shown when the morning block doesn't have empty cells and the afternoon one does. The evening block is shown when the afternoon block doesn't have empty cells and the evening one does.

In the screenshot evening (🌌 Avond) is shown. Morning and afternoon have been completed for this day.  
This gives me a nice overview of my plans for the day, without overwhelming me.

## Notes
The next query is for notes (📜 Notities). It pulls the information from the notes section of today's journal page (this section was shown in my [previous blogpost](../new-daily-log)).  

In fact, this is a bit of a cheeky section. Instead of just showing data, it actually serves as a quick capture. As the query returns the actual block of data, it can be edited here on the start page. The query only returns the last block of the data, which I make sure is always an empty block. Thus, I can type in data under notes, which is edited both here and on the journal page. With a double enter, a new block is added and the query refreshes to only show this new empty block, ready to accept a new quick capture.

Here you can see the notes query.
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

## Activities
The next query is for activities (⏳ Activiteiten). It also pulls the information from today's journal page (this section was shown in my [previous blogpost](../new-daily-log)), yet uses the activities block.  

In fact, it is almost identical to the notes query, except it shows all child blocks at all times. This way I can be sure that I don't accidentally add the same activity twice on the same day.

Here is the activities query.
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

The food (🍽) and [redacted] query works similarly to the activities query. Get specific blocks from today's journal page. It just gets the blocks itself instead of child blocks and thus lacks the `[?b :block/parent ?par]` line and just returns `?par`.

## Breaks and trackers
The breaks and trackers (🕯️ en ✴️) query also pulls data from today's journal page. For breaks (🕯️) it pulls specific blocks, just like the notes, activities and food queries. For trackers it pulls in all blocks with properties, which as shown in my [previous blogpost](../new-daily-log) are now consolidated under the block ✴️ Trackers.

One annoying thing with properties is that for the simple query syntax `(property ?b :name "value")` the `"value"` part must be filled. When left to `""` it doesn't select those blocks with an empty value. Instead, it selects all blocks with that property regardless of value. This is why my query is not using that syntax throughout.  
This query will show different blocks based on different times and conditions. Explanation in the comments of the query.

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
The tasks (Taakjes) query is my task list for the day, with a small twist. It will only list those tasks that are either scheduled for today or unscheduled on today's journal page. And then only those tasks that have not been planned into my weekplanning. I use markdown links `[]()` for that (see "Bridges & Twists" in the screenshot, it is underlined).

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

## Focus this week
The next query to show is focus this week (Focus deze week). As mentioned in [Bullet Journaling in Logseq - Part 1, Week review](../bullet-journaling-in-logseq/#week-review), each week I do a week review. Lately, I have added a focus for the coming week. This query shows this focus.

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

Alternatively, I could get the month page that belongs to today directly. However, my month pages are based on full weeks. So for example November 1st 2023 is actually on the October page and not the November page. The first day of a week determines the page I put it on.

## Later today
The last query is Later today (🗓 Later vandaag). Where the previous section, 🗓 Planning, resulted in showing the current part of the day (morning, afternoon or evening), this query shows the upcoming parts of the day. So for example, when 🗓 Planning shows the morning, this section shows the afternoon and evening. This way the 🗓 Planning section is less overwhelming.

```clojure
#+BEGIN_QUERY
{:title [:h4 "🗓️ Later vandaag"]
 :inputs [:today]
 :query [:find (pull ?b [*])
  :in $ ?dag
  :where
   [?w :block/name "weekplanning"] ;page with name weekplanning
   [?pb :block/page ?w] ;blocks that are on page weekplanning
   [?pb :block/refs ?p] ;this block references ?p
   [?p :block/journal-day ?dg] ;?p defined as the journal page with day ?dg
   [(<= ?dg ?dag)] ;?dg needs to be equal or smaller than today
   ;block that is returned
   [?b :block/parent ?pb] ;?b has the block on page weekplanning as parent
   [?b :block/content ?c] ;get the content for the block and set it to ?c
   ;block for the restriction of the dataset
   [?fb :block/parent ?pb] ;?fb has the block on page weekplanning as parent
   [?fb :block/content ?fc] ;get the content for the block and set it to ?fc
   (or
     ;show afternoon plan
     (and 
       [(clojure.string/includes? ?fc "🌅 Ochtend")] ;?fc includes this text
       [(clojure.string/includes? ?fc "| |")] ;?fc includes an empty cell
       [(clojure.string/includes? ?c "🌄 Middag")] ;?c includes this text
       [(clojure.string/includes? ?c "| |")] ;?c includes an empty cell
     )
     ;show evening plan
     (and 
       (or ;?fc includes either text
         [(clojure.string/includes? ?fc "🌅 Ochtend")]
         [(clojure.string/includes? ?fc "🌄 Middag")]
       )
       [(clojure.string/includes? ?fc "| |")] ;?fc includes an empty cell
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

## Finally
The "Meer..." (more) link at the bottom is a simple markdown link to the contents page. I mainly use this on mobile which doesn't have a sidebar in portrait mode.

And that's my start page. Giving me a solid overview I can look at, throughout the day.