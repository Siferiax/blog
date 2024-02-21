---
title: Easy access using the Contents page
date: 2024-02-20
series:
  - How queries support my Logseq workflow
tags:
  - Logseq
TocOpen: false
draft: true
---
This blog is quickly becoming an archive of how I used to do things. Since the [last post about my Startpage](../start-page-and-queries) it has continued to change. And so too has my contents page.

I use the default Contents page that comes with Logseq as it will open by default when you open the right sidebar. I use it as an extension of the Startpage.
This means it holds both the same information as the Startpage, as well as a lot of extras.
Let's have a look.

![contentpage.png](contentpage.png)

That's a long page!! :)

The first part should be familiar, as it is a copy of queries from my Startpage. Trackers, notes, breaks and activities. As you can see I split off trackers and breaks into separate queries.
I've also stopped tracking so much, so no food query.

After that comes a lot of tasks queries. 3 for open tasks and 2 for completed ones.
Task management is a big topic and I wish to do a more in dept post on it in the future, so for now I'll give a simple run down.
- ☑️ Gepland (planned) are those tasks that I scheduled for today (or earlier) and that I put into today's plan.
- ☑️ Bonus are tasks scheduled for today (or earlier) that aren't on today's plan.
- ☑️ Aangemaakt (created) are tasks that are newly created in today's journal without a scheduled date (yet)
- ✅ Gedaan (completed) are repeating tasks I've completed today. 
- ✅ Afgerond (finished) are tasks that are marked done and are in today's journal or have a reference present in today's journal

🌟 Hobby activiteit (hobby activity) is a folded block that contains a list of activities I like to do. It serves as both a quick link list to my hobby pages and a reminder for myself, so I can easily check and pick an activity if necessary.

Next we have another friend from the Startpage, my planning for today.
That's the last piece of information the two pages share in common.
After this is information only available on the contents page. Useful information that I don't need to see all the time, but that I do check regularly.
I have ordered the information based on how often I wish to check it, or how frequently I use it.

## Tijd pagina's (time pages)
![tijdpaginas.png](tijdpaginas.png)
This year (2024) I'm experimenting with a different way to organize time.
The unit of time I mostly work with is a week. And in my experience a month is just too short.
So I decided to block my weeks into 6 weeks, which forms one cycle.
3 of those cycles form a period. As such I have created a period page, which holds 3 cycles and each week now has its own page.
For easy navigation that is dynamic I created a query which results in a link to the current period page and the current week page. Both pages have a start and end date property which the query uses.

![periode1.png](periode1.png)

```clojure
#+BEGIN_QUERY
{:title [:b "Tijd pagina's"]
 :inputs [:today] ;I want the pages based on today, so that's my input
 :query [:find (pull ?w [*])
  :in $ ?today
  :where
   [?w :block/properties ?prop] ;an entity with properties, can be a block or a page
   [(get ?prop :datum-begin) ?begin] ;that has a property :datum-begin and the value is saved in ?begin
   [(get ?prop :datum-eind) ?eind] ;that has a property :datum-eind and the value is saved in ?eind
   [(<= ?begin ?today ?eind)] ;?begin is smaller or equal to ?today and ?today is smaller or equal to ?eind
 ]
 :view (fn [result] (for [r result] [:div [:a.tag.mr-1 {:href (str "#/page/" (clojure.string/replace (get r :block/original-name) "/" "%2F") )} (get r :block/original-name) ] ] ) ) ;Show the results as a :a.tag.mr-1 link within a :div (this is hiccup), the a.tag.mr-1 looks are specified in custom.css. The / in the results is replaced with %2F to make the link work
}
#+END_QUERY
```

## 🎯 Deze week (this week)
![dezeweek.png](dezeweek.png)
This simply shows the goals I have set for this week. Either as a task to complete, or simply a more general focus.
These come from my week pages.

```clojure
#+BEGIN_QUERY
{:title [:h4 "🎯 Deze week"]
 :inputs [:today]
 :query [:find (pull ?doel [*])
  :in $ ?today
  :where
   [?w :block/properties ?prop]
   [(get ?prop :datum-begin) ?begin]
   [(get ?prop :datum-eind) ?eind]
   [(<= ?begin ?today ?eind)]
   [?header :block/page ?w]
   [?header :block/content ?hc]
   [(clojure.string/includes? ?hc "🎯 Week Doelen")]
   [?doel :block/parent ?header]
 ]
 :result-transform :sort-by-list
 :breadcrumb-show? false
}
#+END_QUERY
```

## 🗓️ Rest van de week (rest of the week)
![restweek.png](restweek.png)
These days I use a simplified week overview to see the bigger picture and plan out the week.
I figured out a rather sneaky way to use markdown tables. If you add embedded blocks to them, you can have multiple lines within one table cell.
With the added benefit of being able to create a table anywhere and being able to edit it in place and have all other tables change accordingly. This has been a game changer for my planning.
Each day of the week has a number of blocks associated with it. I made a table with all days of the week as an overview, but I also created tables for each day separately. Those you see in the query result.
The naming of "2.Thu" is deliberate. The 2 is used for sorting and the "Thu" is used by the query for finding the days I want to show.
As my coach comes around on Tuesday, my weeks are planning Wed-Tue. As said the blocks are for each weekday, this means they are not for each date. The blocks are the same whether it is Monday January 1st or Monday January 7th. So I don't want to see things beyond Tuesday as that would be blocks that have already happened.
This query uses some weird trickery. 

```clojure
#+BEGIN_QUERY
{:title [:b "🗓️ Rest van de week"]
 :inputs [:today :tomorrow :+1d :+2d :+3d :+4d :+5d :+6d :+7d]
 :query [:find (pull ?b [*])
  :in $ ?vandaag ?morgen ?een ?twee ?drie ?vier ?vijf ?zes ?zeven
  :where
   [?v :block/journal-day ?vandaag]
   [?v :block/original-name ?vandaagnm]
   [(subs ?vandaagnm 0 3) ?vdag]
   (or
     (and
       [(= ?vdag "Mon")]
       [(ground ?een) ?week]
     )
     (and
       [(= ?vdag "Tue")]
       [(ground ?zeven) ?week]
     )
     (and
       [(= ?vdag "Wed")]
       [(ground ?zes) ?week]
     )
     (and
       [(= ?vdag "Thu")]
       [(ground ?vijf) ?week]
     )
     (and
       [(= ?vdag "Fri")]
       [(ground ?vier) ?week]
     )
     (and
       [(= ?vdag "Sat")]
       [(ground ?drie) ?week]
     )
     (and
       [(= ?vdag "Sun")]
       [(ground ?twee) ?week]
     )
   )
   [?j :block/journal-day ?dag]
   [(<= ?morgen ?dag ?week)]
   [?j :block/original-name ?journalnm]
   [(subs ?journalnm 0 3) ?jdag]
   [?w :block/name "weekplanning"]
   [?pb :block/page ?w]
   [?pb :block/content ?pc]
   [(clojure.string/starts-with? ?pc "Block embeds")]
   [?b :block/parent ?pb]
   [?b :block/content ?c]
   [(clojure.string/includes? ?c ?jdag)]
 ]
 :breadcrumb-show? false
 :group-by-page? false
 :result-transform (fn [result] (sort-by (fn [r] (get r :block/content)) result))
}
#+END_QUERY
```

## 🗓️ Deze Cyclus (this cycle)
![cyclus.png](cyclus.png)
This query displays the intentions I set for the current cycle. These can be more akin to goals, represented by tasks, but also things to focus on. I try to write my goals as "do this activity" rather than "finish this thing". For example I want to do 7 play sessions of Tears of the Kingdom. Length and what I do in those sessions doesn't matter.

```clojure
#+BEGIN_QUERY
{:title [:h4 "🗓️ Deze Cyclus"]
 :inputs [:today]
 :query [:find (pull ?b [*])
  :in $ ?today
  :where
;bepaal welke cyclus het is
   [?p :block/properties ?prop]
   [(get ?prop :cyclus) ?num]
   [(str "Cyclus " ?num) ?current]
   [(get ?prop :datum-begin) ?begin]
   [(get ?prop :datum-eind) ?eind]
   [(<= ?begin ?today ?eind)]
;haal de intentie van de cyclus op
   [?periode :block/properties ?pp]
   [(get ?pp :datum-begin) ?pbegin]
   [(get ?pp :datum-eind) ?peind]
   [(<= ?pbegin ?today ?peind)]
   [?periode :block/name ?pnm]
   [(clojure.string/includes? ?pnm "periode")]
   [?cy :block/page ?periode]
   [?cy :block/content ?cyclus]
   [(clojure.string/includes? ?cyclus ?current)]
   [?int :block/parent ?cy]
   [?int :block/content ?i]
   [(clojure.string/includes? ?i "# Intentie")]
   [?b :block/parent ?int]
 ]
 :breadcrumb-show? false
 :group-by-page? false
}
#+END_QUERY
```

## 🌋 Projecten (projects)
![projecten.png](projecten.png)
I have a title that is a link to my projects page. The actual query is below that with the title Actief (active). I like to always give a title to my queries.
The result is a simple list of projects that have the status active.

```clojure
#+BEGIN_QUERY
{:title [:b "Actief"]
 :query [:find (pull ?p [*])
  :where
   (page-property ?p :type "project")
   (page-property ?p :status "actief")
 ]
 :result-transform (fn [result] 
  (sort-by 
    (juxt 
      (fn [r] (get-in r [:block/properties :prio])) 
      (fn [r] (if (= "" (get-in r [:block/properties :datum-grens])) "9999-99-99" (get-in r [:block/properties :datum-grens]))) 
      (fn [r] (get r :block/name)) 
    ) 
    result
  )
 )
 :view (fn [result] (for [r result] [:div [:a.tag.mr-1 {:href (str "#/page/" (get r :block/original-name) )} (str (get-in r [:block/properties :prio]) " : " (get r :block/original-name) ) ] ] ) )
}
#+END_QUERY
```

## Notities (notes)
![notities.png](notities.png)
Queries related to my note taking. In all honestly I barely check this section, so no wonder it is all the way at the bottom.
I've written [a little bit about my note taking in the past](../../musings/changing-note-taking). I use different types of notes. I have plain notes I add to my journal, just thoughts and feelings about stuff, I don't generally label those or give them a template. Those fall outside of this whole section.
This section is for bigger notes. Ideas, and notes about articles, things to further explore later.

### Vandaag (today)
The first query simply shows any notes I made today. 
```clojure
#+BEGIN_QUERY
{:title [:h4 "📜 Vandaag"]
 :inputs [:today]
 :query [:find (pull ?b [*])
  :in $ ?dag
  :where
   [?j :block/journal-day ?dag]
   [?b :block/refs ?j]
   [?b :block/refs ?p]
   [?p :block/name ?n]
   [(contains? #{"📚lessen" "🧭verkenning" "🔍uitzoeken" "🧿inzicht"} ?n)]
 ]
 :breadcrumb-show? false
 :group-by-page? false
}
#+END_QUERY
```

### Inbox
A count of blocks present on my inbox pages. Mostly used for highlights I shared to Logseq that I haven't processed yet.
```clojure
#+BEGIN_QUERY
{:title [:h4 "📥 Inbox"]
 :query [:find ?name ?link (count ?b)
  :keys page link count
  :where
   [?i :block/name "inbox"]
   [?i :block/original-name ?link]
   [(str "Aantal") ?name]
   [?b :block/page ?i]
   (not (has-property ?b :icon))
 ]
 :view (fn [rows] (for [r rows] [:div [:a {:href (str "#/page/" (get r :link) )} (str (get r :page) " : " (get r :count) )] ] ))
}
#+END_QUERY
```

### In uitvoering (under construction)
A count of notes that are under construction, that I need to work on more. The count is per note type.
```clojure
#+BEGIN_QUERY
{:title [:h4 "🚧 In uitvoering"]
 :query [:find ?name (count ?b)
  :keys page count
  :where
   [?t :block/name ?name]
   [(contains? #{"🧭verkenning" "🔍uitzoeken"} ?name)]
   (property ?b :status ?name)
   [?b :block/page ?p]
   (not [?p :block/name "templates"])
 ]
 :result-transform (fn [result] 
  (sort-by
    (fn [x] (
      (into {}
        (map-indexed
          (fn [i e] [e i]) ["🧭verkenning" "🔍uitzoeken"]
        ) ;;=> ([d 0] [i 1] [u 2])
      ) ;;=> {d 0, i 1, u 2}
      ((fn [r] (get r :page)) x)
    )
  ) ;;=> Get value of :page from result (through x and r). 
      ;;=> Lookup that value in the map created through map-indexed and into. 
      ;;=> Sort result based on that value index number pair.
  result)
 )
 :view (fn [rows] (for [r rows] [:div [:a {:href (str "#/page/" (get r :page) )} (str (get r :page) ": " (get r :count) )] ] ))
}
#+END_QUERY
```

### Random
A random note that is under construction. As a reminder of a random note, hopefully to inspire me to work on it. Sadly it doesn't quite work as I hardly check here.
```clojure
#+BEGIN_QUERY
{:title [:h4 "Random 🚧"]
 :query [:find (pull ?b [*])
  :where
   (has-property ?b :status)
   (or
     (property ?b :status "🧭verkenning")
     (property ?b :status "🔍uitzoeken")
   )
   [?b :block/page ?p]
   (not [?p :block/name "templates"])
 ]
 :result-transform (fn [result] [(rand-nth (map (fn [m] (assoc m :block/collapsed? true)) result))])
 :breadcrumb-show? false
 :group-by-page? false
}
#+END_QUERY
```