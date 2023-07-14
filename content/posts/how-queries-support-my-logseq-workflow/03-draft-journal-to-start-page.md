---
title: "From Journal pages to the Start Page"
date: 2023-08-01
series: 
  - How queries support my Logseq workflow
tags: 
  - Logseq
TocOpen: false
draft: true
---
One of the biggest things I love about Logseq is how I can surface information with queries. And I keep finding new information that is useful for me and I'd like to query.

To help me create structure and overview, I was writing more and more queries. Having them on pages and trying to cram them into the config file for easy access.

Very quickly this wasn't workable anymore. So I decided to make a start page instead of using the journal as my default home.

This page is still not done as I keep finding new ways to optimize how I work. Or rather I keep finding ways that don't work for me and then tweak them!

~~At the time of writing (2023-01-28) my start page is structured as follows~~ Yeah no it's now the 4th of February and this page just keeps changing 😅

Query to get the journal page of today. If you want a copy of your journal page on any other page, this would be all you need. I still want it a bit more fancy, but for that I'm waiting on the implementation of time: https://github.com/logseq/logseq/pull/8387
```Clojure 
#+BEGIN_QUERY
{:query [:find (pull ?b [*])
   :in $ ?dag
   :where
     [?p :block/journal-day ?dag]
     [?b :block/page ?p]
 ]
 :table-view? false
 :inputs [:today]
}
#+END_QUERY
```

Next is my planning section for the next day. I used to do this on today's journal page, but recently pulled it over to my start page. For each new day I call my template tomorrow, which fills this section with the Morning, Afternoon, Evening sections and then beneath those all standard tasks underneath a bullet for the day they are planned. For example:
### Thursday
TODO Groceries & garbage.

I also have two standard queries in my planning section. One for tasks that are planned, but not standard (= repeats weekly).
```clojure
#+BEGIN_QUERY
{:title [:h3 "Taken"]
 :query [:find (pull ?b [*])
 :in $ ?dag
 :where
   [?b :block/marker "TODO"]
   (or [?b :block/scheduled ?dag]
   [?b :block/deadline ?dag])
 ]
 :result-transform (fn [result] (sort-by (juxt (fn [r] (get-in r [:block/scheduled])) (fn [r] (get-in r [:block/deadline])) ) result))
 :table-view? false
 :breadcrumb-show? false
 :inputs [:tomorrow]
}
#+END_QUERY
```

And one for my agenda. I use a separate digital calendar. However, I wish to pull everything together and for this I use a table I generate in a spreadsheet that I have in my monthly pages for each week. The current week also has a block reference to the week after.
```clojure
#+BEGIN_QUERY
{:title [:h3 "Agenda"]
 :query [:find (pull ?b [*])
   :in $ ?dag
   :where
     [?p :block/journal? true]
     [?p :block/journal-day ?dag]
     [?b :block/refs ?p]
     [?b :block/page ?pg]
     [?pg :block/namespace ?ns]
     [?ns :block/name "2023"]
  ]
  :table-view? false
  :inputs [:today]
}
#+END_QUERY
```

Next is a query that gets any tasks left unchecked yesterday. Only those on yesterday's journal page, either scheduled for yesterday or not scheduled at all. These are the tasks I need to take action on, either by doing them today or (re)scheduling them. This is something new I added today actually.

And I already changed it before I finished writing this. It is now 3rd of February. I added the planning section up top ☝🏼 and added deadline filters to the open from yesterday section. This addition happened because I did a braindump for lots of tasks and gave those deadlines and not scheduled dates. They all ended up in this query result while not being something I had to deal with.
```clojure
#+BEGIN_QUERY
{:title [:h3 "🔥 Open van gisteren"]
 :query [:find (pull ?b [*])
 :in $ ?day
 :where
   [?p :block/journal-day ?day]
   [?b :block/page ?p]
   [?b :block/marker "TODO"]
   (or
     [?b :block/scheduled ?day]
     (not [?b :block/scheduled])
   )
   (or
     [?b :block/deadline ?day]
     (not [?b :block/deadline])
    )
 ]
 :inputs [:yesterday]
 :table-view? false
}
#+END_QUERY
```

The last query for this part of my start page is all my goals for the week that are lacking a scheduled date.
```clojure
#+BEGIN_QUERY
{:title [:h3 "🎯 Nog niet gepland"]
 :query [:find (pull ?b [*])
 :in $ ?day
 :where
   [?b :block/deadline ?d]
   [(<= ?d ?day)]
   [?b :block/marker "TODO"]
   (not [?b :block/scheduled _])
]
  :result-transform (fn [result] (sort-by (fn [r] (get-in r [:block/deadline])) result))
  :breadcrumb-show? false
  :table-view? false
  :inputs [20230205]
}
#+END_QUERY
```

Yes the manual date is a little clunky, but I need it to be this week's Sunday.

After my most immediate concerns of the day, I fill my start page with extra things to do. Things I want to get to when time permits. It's inspiration for how to spend my time instead of doing the easy thing like browsing YouTube. The first thing I will look at is my intentions for this month. They are my guide for what I prioritized for this month. Generally they are broad guidelines. For example one of my January intentions was to watch less random YouTube videos.

I write my intentions on my monthly page and then do a block embed on my start page as well as my yearly page.

After that I have a query which list all the weekly goals that do have a scheduled date. Just as a "this is incoming" kind of reminder. I like being able to easily view that, and this is the location for right now.
```clojure
#+BEGIN_QUERY
{:title [:h3 "🎯 Gepland"]
 :query [:find (pull ?b [*])
 :in $ ?day
 :where
   [?b :block/deadline ?d]
   [(<= ?d ?day)]
   [?b :block/marker "TODO"]
   [?b :block/scheduled _]
 ]
 :result-transform (fn [result] (sort-by (juxt (fn [r] (get-in r [:block/scheduled])) (fn [r] (get-in r [:block/deadline])) ) result))
 :breadcrumb-show? false
 :table-view? false
 :inputs [20230205]
}
#+END_QUERY
```

Random insights  
This next part has 2 queries, one personal and one more PKM related. As I record things as live my life in general I may have an insight on my personal life. I tag those as "revelation" which is a namespace with (right now) two subpages: learning and practicing. An insight is either "done", I learned something about myself. Or it is something I wish to learn or practice.

The second type of insight is a piece of general information that resonated strongly with me. I tag those as "insight".

The queries will grab a random insight of the tag (one for revelation and one for insight) to display. This makes those insight reappear. I read them and if I have any thoughts on them I will journal about it.

This is my way of slowly adding to my learnings.
```clojure
#+BEGIN_QUERY
{:title [:h3 "Random 🔮openbaring"]
  :query [:find (pull ?b [*])
	           :where
               [?p :block/name "🔮openbaring"]
	           [?b :block/refs ?p]
               [?b :block/page ?s]
               (not [?s :block/name "mijn workflow"])
  ]
  :result-transform (fn [result] [(rand-nth result)])
  :breadcrumb-show? false
  :table-view? false
}
#+END_QUERY
```
```clojure
#+BEGIN_QUERY
{:title [:h3 "Random 🧿inzicht"]
  :query [:find (pull ?b [*])
	           :where
               [?p :block/name "🧿inzicht"]
	           [?b :block/refs ?p]
               [?b :block/page ?s]
               (not [?s :block/name "mijn workflow"])
  ]
  :result-transform (fn [result] [(rand-nth result)])
  :breadcrumb-show? false
  :table-view? false
}
#+END_QUERY
```

Starting this year I decided to make a multi year journal (inspired by [JashiiCorrin](https://youtu.be/PVUQISP6pX8) on YouTube). I have this as a physical journal, but to prevent myself from not using it I also add a small summary of the day at the bottom of my journal. This I will then later process into my physical one. I start the section with `#📓dagboek/verwerken` and remove `/verwerken` when I wrote it down in my physical journal.

On my start page I list all entries I still need to write down except the entry for today.
```clojure
#+BEGIN_QUERY
{:title [:h3 "📓dagboek verwerken"]
  :query [:find (pull ?b [*])
               :in $ ?dag
               :where
               [?db :block/name "📓dagboek/verwerken"]
               [?b :block/refs ?db]
               [?b :block/page ?p]
               [?p :block/journal? true]
               [?p :block/journal-day ?d]
               [(< ?d ?dag)]
  ]
  :table-view? false
  :inputs [:today]
}
#+END_QUERY
```

After this the whole next part of the page is dedicate to things I someday maybe want to take care of or do. Many related to my hobbies.

First I use some specific tags to indicate I want to work on this further. I use another type of insight tag for thoughts and ideas. And I use a tag for things I want to research.

The queries I use result in a simple link with a count behind it. The link leads to another query which shows the actual blocks related to the tag. (this has to do with how `x results` is shown)
```clojure
#+BEGIN_QUERY
{:query [:find (count ?b)
              :keys count
              :where
              [?i :block/name "💡ingeving"]
              [?b :block/refs ?i]
              [?b :block/page ?p]
              (not [?p :block/name "mijn workflow"])
  ]
  :view (fn [rows] (for [r rows] [:a {:href "#/page/63be6d79-4646-4110-85a9-ff34a644629b"} [:h3 (str "💡ingeving: " (get r :count) )] ] ))
}
#+END_QUERY
```

I do the same for resources I still want to process further and personal insights I want to learn.
For the resources the query is slightly different.
```clojure
#+BEGIN_QUERY
{:query [:find (count ?p)
              :keys count
              :where
              [?t :block/name "resources"]
              [?p :block/tags ?t]
              [?p :block/properties ?prop]
              [(get ?prop :state) ?state]
              [(contains? #{"new" "distilling"} ?state)]
]
  :view (fn [rows] (for [r rows] [:a {:href "#/page/63be68ca-b940-4629-8f18-e98cd5a4d5ba"} [:h3 (str "🗂 Resources te verwerken: " (get r :count) )] ] ))
}
#+END_QUERY
```

Next I have my areas of responsibility. Which is a simple tag list.
```clojure
#+BEGIN_QUERY
{:title [:h3 "📌 Areas"]
 :query [:find ?name
              :where
              [?p :block/original-name ?name]
              [?p :block/tags ?t]
              [?t :block/name "areas"]
     ]
  :result-transform (fn [result] (sort result))
  :view (fn [tags]
           [:div
           (for [tag (flatten tags)]
                [:a.tag.mr-1 {:href (str "#/page/" tag)}
                (str "#" tag)]
            )]
    )
}
#+END_QUERY
```

Then a bunch of tables and `{{namespace }}` queries for my activities.

More interestingly though I made a query to get when I last did one of my activities. This one had given me quite the headache at the time! It became a lot easier after I restructured my activities to use namespaces.
```clojure
#+BEGIN_QUERY
{:title [:h3 "Laatste keer"]
 :query [:find ?act ?d ?dag
 :keys act day dagnm
 :where
 [?t :block/name "activiteiten"]
 [?a :block/tags ?t]
 [?a :block/name ?act]
 [?r :block/namespace ?a]
 [?b :block/refs ?r]
 [?b :block/page ?p]
 [?p :block/original-name ?dag]
 [?p :block/journal-day ?d]
 ]
 :result-transform (fn [result] 
      (map (fn [[key value]] {:page key :val (first value)})
          (group-by (fn [r] (get-in r [:act])) 
               (sort-by (fn [r] (get r :day)) > result) 
           ) 
       )
 )
 :view (fn [rows] [:table [:thead [:tr  
    [:th "Activiteit"] [:th "Laatste keer"] ] ] 
 [:tbody (for [r rows] [:tr 
    [:td [:a {:href (str "#/page/" (clojure.string/replace (get-in r [:page]) "/" "%2F"))} (get-in r [:page])] ]
    [:td [:a {:href (str "#/page/" (get-in r [:val :dagnm]))} (get-in r [:val :dagnm])] ]
 ] ) ]
 ] )
 :collapsed? false
}
#+END_QUERY
```

After all that, I embed my `Do in <year>` section from my yearly page. To keep that sort of at the forefront?! By now we scrolled down so far, that we can put that into question!

To really close of the page I have a query that gets all media I want to consume at some point.
```clojure
#+BEGIN_QUERY
{:title [:h3 "Media voor later"]
  :query [:find (pull ?b [*])
          :where
          [?t :block/name "activiteiten"]
          [?s :block/tags ?t]
          [?p :block/namespace ?s]
          [?b :block/page ?p]
          [?b :block/marker "LATER"]
  ]
  :table-view? false
}
#+END_QUERY
```

To be really honest, this page has just gotten too big to be 100% useful. As I've been trying to write this blogpost, my way of working has changed. I keep shifting how I organise and do things, because I'm still figuring out what works best. I still don't know what information needs to be served where and how I can keep on top of things I wish to do.

Still the items are ordered very specifically. First I want to tackle everything for today, then anything left over from yesterday, and so on and so forth.

This helps give me structure. Mostly I just look at the top of the page and a number of sections (like for example tomorrow's plan) are almost always collapsed. Still if I want to get an idea of what I have left unfinished, I can scroll through just this one page instead of looking at several.
