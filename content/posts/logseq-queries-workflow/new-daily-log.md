---
title: Changing my daily log
date: 2023-10-29
series:
  - How queries support my Logseq workflow
tags:
  - Logseq
TocOpen: false
draft: false
---
One of my biggest joys in Logseq are the queries. By this time they are deeply integrated in how I operate. In the [previous post](../bullet-journaling-in-logseq2) I showed how my daily was set up. I also mentioned I simplified it.

In fact, as I have learned more tricky queries, I have been able to simplify a lot of my workflow. I no longer move my planning around, instead I utilize a start page to show it while it isn't on my journal page at all.  
But, I'm getting ahead of myself!  
My start page deserves [its own blogpost](../start-page-and-queries), here I will exclusively focus on the new way I set up my daily log on the journal pages.

All queries I use on the journal pages are included at the end of the blogpost.

Here's a before and after of a Friday. Both use the same data, just on different days.

![old-vs-new.png](old-vs-new.png)

It is a little bit difficult to see, so let's focus on one section at a time.
## Page properties
![page-properties.png](page-properties.png)
In the old daily I utilized a bunch of page properties. I got rid of the month property in favor of getting the month in the queries themselves (this will be its own blogpost). And the rest went to the trackers section I explain below.
## Dagboek
![dagboek.png](dagboek.png)
This part is now automated with queries. All activities with the symbol ✨ in front get shown below the ✨ Highlights query. Everything that is a hobby gets listed under the ⛰ Hobbies query.  
💪🏼 Trots op / 🙏🏼 Dankbaar voor (proud of / grateful for) was moved from the 🧠 Braindump section to here and changed from a table to a list. (see the screenshot below for what the 🧠 Braindump section looked like)
## Braindump and notes
![braindump-notes.png](braindump-notes.png)
🧠 Braindump has simply been rebranded to 📜 Notities (notes). There is no longer any default content, just whatever notes come up during the day are recorded here. This includes things that happened during the day, but that weren't "a thing I did".  
In this example "Something stressful happened". Though also something like how I felt getting out of bed, things like that.
## Today's schedule and activities
![schedule-activities.png](schedule-activities.png)
The schedule still exists, but I no longer move it. All the notes I made beneath it, received a new place. Most noteably for this are the activities (⏳ Activiteiten). Simply, what things did I do during the day. I no longer care whether I watched youtube in the morning, or both in the morning and afternoon. I watched youtube this day.  
I thought to myself, if I read this a year from now, what notes would matter? In a year's time I probably wouldn't care which part of the day something went on. And if I do, I can always note that down manually.  
Those activities that were important, or I spend most of the day doing, will get a ✨ symbol to indicate they are highlights. Highlights are mainly used for [my weekly review process](../bullet-journaling-in-logseq/#week-review).
## Breaks
![breaks.png](breaks.png)
Instead of being in between other notes with a 🕯 signifier, breaks now have their own space. I still use the morning (O), afternoon (M) and evening (A) separation here. This gets used mainly on my start page. Here I will record whether I skipped (❌) a break, or what I did during the break.
## Repeating tasks
![herhaal-taakjes.png](herhaal-taakjes.png)
A query which will show me the repeating tasks I crossed off. This uses the Timetracking feature of Logseq which gives (repeating) tasks a logbook. (there's nuance I'm not getting into here). The query shows a custom view list instead of all information on a task. This keeps it concise.
## Food tracking
![food.png](food.png)
The property has been moved to the ✴ Trackers section. Otherwise, this has basically remained the same outside of formatting. 
## All other trackers
![trackers.png](trackers.png)
Here are all the properties I was using divided in a few specific blocks. I renamed some to symbols as that was easier for me.

## Queries
### Dagboek
Highlights query
```clojure
#+BEGIN_QUERY
{:title [:b "✨ Highlights"]
 :inputs [:query-page] ;lower-case name of the page the query is on
 :query [:find ?c ;return the content of the block
  :in $ ?page
  :where
   [?p :block/name ?page] ;set ?p to the page the query is on
   [?b :block/page ?p] ;blocks on this page
   [?b :block/content ?c] ;the content of those blocks
   [(clojure.string/includes? ?c "✨")] ;the content includes this text
   (not [(clojure.string/includes? ?c "#+BEGIN_QUERY")]) ;the content doesn't include this text, aka it is not this query
 ]
 :breadcrumb-show? false ;don't show the parent blocks
 :group-by-page? false ;don't group the results by page
 :view (fn [result] ;define a custom view
  (for 
    [r result ;for each r in result (so each :block/content see find)
      :let [one (first (str/split-lines r))] ;split the content into lines and get the first only
      :let [two (str/replace (str/replace one "[[" "") "]]" "")] ;remove any double brackets by replacing them with an empty string
      :let [three (str/replace two "DONE " "")] ;replace the word DONE
      :let [item (str/replace (str/replace three "✨ " "") "✨" "")] ;replace the highlight symbol. First including the space and then again. Just a little quirck of my data!
    ] 
    [:li item] ;display the result of all the above using a list
  )
 )
}
#+END_QUERY
```

Hobbies query
```clojure
#+BEGIN_QUERY
{:title [:b "⛰️ Hobbies"]
 :inputs [:query-page] ;lower-case name of the page the query is on
 :query [:find (pull ?r [*])
  :in $ ?page
  :where
   [?p :block/name ?page] ;set ?p to the page the query is on
   [?b :block/page ?p] ;blocks on this page
   [?b :block/refs ?r] ;blocks with a reference of ?r
   (page-property ?r :cluster "hobby") ;define ?r as pages with the page property cluster:: hobby
 ]
 :breadcrumb-show? false ;don't show the parent blocks
 :group-by-page? false ;don't group the results by page
 :result-transform (fn [result] (sort-by (fn [r] (get r :block/name)) result)) ;sort the result based on lower-case page name
 :view (fn [result] (for [r result] (str (get r :block/original-name) ". ") ) ) ;show the result as a string of the page's original name with a . at the end
}
#+END_QUERY
```

Side-note: using the `(page-property)` syntax instead of the advanced query syntax `[?r :block/properties ?prop]` has the advantage that it doesn't matter whether you write your value as plain text or a page reference (hobby or `[[hobby]]` in the above query).
### Repeated tasks
```clojure
#+BEGIN_QUERY
{:title [:mark "✅ Herhaal taakjes"]
 :inputs [:query-page] ;lower-case name of the page the query is on
 :query [:find (pull ?b [*])
  :in $ ?page
  :where
   [?p :block/name ?page]
   [?p :block/journal-day ?dag]
   [(str ?dag) ?strdag]
   [(subs ?strdag 0 4) ?jt]
   [(subs ?strdag 4 6) ?mt]
   [(subs ?strdag 6) ?dt]
   [(str "* State \"DONE\" from \"TODO\" [" ?jt "-" ?mt "-" ?dt) ?strtodo]
   [(str "* State \"DONE\" from \"DOING\" [" ?jt "-" ?mt "-" ?dt) ?strdoing]
   [?b :block/marker _]
   [?b :block/content ?c]
   (or 
     [(clojure.string/includes? ?c ?strtodo)]
     [(clojure.string/includes? ?c ?strdoing)]
   )
 ]
 :result-transform (fn [result] (sort-by 
  (juxt 
    (fn [r] (get r :block/priority "X"))
    (fn [r] (get r :block/content))
  ) 
  result
 ) )
 :view (fn [result]
  (for 
    [r result ;for each r in result (so each :block/content see find)
      :let [c (get r :block/content)] ;get the content of the block
      :let [line (first (str/split-lines c))] ;split the content into lines and get the first only
      :let [clean (str/replace (str/replace line "[[" "") "]]" "")] ;remove any double brackets by replacing them with an empty string
      :let [item (str/replace (str/replace (str/replace clean "TODO " "") "[#" "") "] " " : ") ] ;replace the word TODO, and replace the priority of the task, only keeping the letter and then add a : between the letter and the task name/description.
    ] 
    [:li item] ;display the result of all the above using a list
  )
 )
 :group-by-page? false ;don't group the results by page
 :breadcrumb-show? false ;don't show the parent blocks
}
#+END_QUERY
```