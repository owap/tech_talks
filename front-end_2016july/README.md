# Building Performant Web UIs
Or: The best parts of Clojure ported to Javascript
Or: Why can't we just be a Clojure shop?

# What is Performance?
+ 60 FPS
+ 1000ms / 60 Frames = 16ms / frame
+ The goal is to achieve a page update speed of < 16ms
+ Any slower than this, the app feels "janky" (it stutters or freezes intermittently)
+ Users begin to perceive lag at 100ms
+ Web requests + DOM update should be < 100ms
+ All changes to the DOM should be lte 16ms

# Updating the DOM in Angular
+ Two-way data binding...
+ ...via the Digest Cycle...
+ ...which checks every variable over and over...
+ ... and over and over and over for changes.
+ Every `{{expression}}` in views
+ Every `$scope.watch[Collection]()` function

When the digest cycle is triggered, Angular will iterate over every variable it
is watching and perform an object comparison to see if the watched object has changed. If so,
Angular will rebuild the DOM and kick off a second digest cycle to catch any
side effects of the change. This continues until there are no more changes, at
which point Angular stops running digest cycles until another event occurs (such
as user interaction with the view, callback returning and changing a $scope
variable, etc.), at which point we start the process over again.

Let's look at quantifying the expense of the digest cycle:

# Quantifying the Cost of the Digest Cycle:

## Watchers in AlertWhere-UI
+ Map Explorer: 422 (562 with color modal open)
+ Collection Candidates: 498 (582 with filter modal open)
+ Bookmarks: 619 (No bookmarks selected)
+ Content Explorer
  - 814 (News Articles, Today)
  - 567 (Twitter, Arabic Conflict)
  - 993 (Saved Search)
+ Saved Search Builder: 1343 (NAI Selection Screen)

## How Frequently is the Digest Cycle Run?
+ 51 times to load the app on the map view
+ 35 times to switch to the Collection Candidate view
+ 75 times to switch to the bookmarks view
+ 67 times to open the content explorer
+ 105 times to open saved search editor (and select one saved search)

## The Cost of Doing Business in Angular
After logging in, to load the main page, Angular does:
> 422 * 51 = 21,522 object comparisons to build the DOM

Switching to the Collection Candidate view:
> 498 * 35 = 17,430 object comparisons

Opening the Bookmarks view:
> 619 * 75 = 46,425 object comparisons

Opening the Content Explorer:
> 814 * 67 = 54,538 object comparisons

Opening the Saved Search editor:
> 1343 * 105 = 140,805 object comparisons

# Janky Rendering
Ever wonder why all of our animations aren't silky smooth in the application?
That's because it takes longer than 16ms to run tens of thousands of object
comparisons (our digest cycle) to know what the state of the DOM should be. Thus,
we cannot smoothly interpolate between states; we sorta just "jump" to where we
should be at the end of each digest cycle.

# Improving Angular?
Of course, we can always improve our Angular performance. Here are a couple best
practices that yield dividends on the digest cycle:

+ Expressions in the view should avoid function calls to avoid having to run
  that function on every single digest cycle.

  For example, `<div ng-if="shouldRenderDiv()">` will run on every digest cycle,
  but `<div ng-if="myVar">` will not need to be evaluated if the value of myVar
  doesn't change.

  (Basically, every function call adds a new digest cycle watcher, but we're
  already watching all $scope variables, so the latter example doesn't cost us
  extra)

+ Debounce your input fields (so we don't run a digest cycle on every keystroke)

+ Check Google

Most of the performance improvements boil down to: "How can I reduce the number
of watchers on the Digest Cycle, or how can I reduce how frequently the Digest
Cycle is run?" The digest cycle is an inherently expensive way to keep the DOM
up-to-date, but it's the price we pay for having two-way data binding.

# Umm You Mentioned Clojure
Oh! I'm so glad you remembered.

Clojure is a magical and wonderful language, the details of which I will not
bore you with right now. But there is one thing that Clojure does great that I'm
going to talk about: Immutable Data Structures.

# What are Immutable Data Structures?
Immutable Data Structures (or Persistent Vectors) are a data structure invented
by Rich Hickey (and influenced by Phil Bagwell's paper on [Ideal Hash
Trees](http://lampwww.epfl.ch/papers/idealhashtrees.pdf) that give O(1)
performance for appends, updates, lookups, and subvec.

Basically, they're super-performant data structures, because they do not allow
in-place mutation. When you want to update the data structure, references are
copied and pointed at existing variables. Like the history of a project in Git,
changes are applied to the structure, but all the older references and values are
still present (so it's very easy to implement features like "undo").

## Adding
<img src="./persistent_ds2_append.png" width=600 />

## Creating a Second Data Structure:
<img src="./persistent_ds1.png" width=600 />

## More information:
Visit [this site](http://hypirion.com/musings/understanding-persistent-vector-pt-1)
for a crash course in Clojure's persistent vectors.

# Immutable Data Structures on the Front End
Recall that Angular performs anywhere from 14k to 140k object comparisons on every
digest cycle in AlertWhere-UI. What if, instead of deep object comparisons, all
those comparisons happened in O(1) (constant) time?

Actually, Minko Gechev writes in a [blog post](http://blog.mgechev.com/2015/03/02/immutability-in-angularjs-immutablejs/)
about his adventures in using ImmutableJS with Javascript. He benchmarked the
following results:

<img src="./immutable-angular-1.png" width=600 />

**X-axis is the number of bindings (Watchers)**
**Y-axis is the number of milliseconds**

In other words: the more variables that we're keeping track of, the more time is
saved by using immutable data structures. Keep in mind that the number of
watchers in our application is 1-2 orders of magnitude larger than this!  From
the blog post:

> The immutable data helps us speedup the watchers from O(n) (looping over the
> whole data structure) to O(1) (comparing only references, since on change a
> new immutable data structure will be created).

# Recap
+ We want DOM updates to be lte 16ms 
+ Large Angular apps are not good at meeting this requirement due to:
  - Two-way data binding, requiring:
  - The Digest Cycle
    * Which is run over and over
  - ...For hundreds of watched variables
  - Performing whole object comparison rather than reference comparisons
+ Immutable data structures can significantly speed up object comparisons

# Getting Rid of the Digest Cycle
Faster object comparisons doesn't solve our performance problems. The digest
cycle will continue to run and require us to make these comparisons as long as
two-way data bindings exist.

What's the solution to two-way data bindings?

## Uni-directional Data Flow
This shouldn't be a hard sell.  This is the same principle that we use with
Git-flow:

1. Get current state; observe side effects of new data
```
git fetch upstream
git checkout upstream/develop
git checkout -b my_feature
```

2. Function is applied to modify state
```
# Write code
git commit
git push origin
```

3. State is updated as a result of the function
```
# Create pull request
# Perform code review
# Code is merged
```
