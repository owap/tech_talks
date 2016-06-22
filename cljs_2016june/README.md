# Objectives
+ Improve Workflow
+ Improve Code Quality
+ Improve User Experience

# Introduction: The OpenWhere User Interface State of the Union
Developing the user interface is the bottleneck for many of our products.

Developing UIs involves shifting both bits *and* pixels, which is a
time-intensive process. Because it takes longer to develop UIs (generally
speaking) than the backend components that support them, our capability backlog
of backend-supported features piles up, putting pressure on UI developers to
expedite the UI development process.

This time pressure, coupled with the fact that we have no customers embedded on
the product team, means that insufficient time is given to usability and user
experience.  We're inclined to say "good enough" to the features we develop,
because they'll be presented to users at some point in the future, and we'll
improve upon those features even further down the road.

Without active engagement and tight feedback loops, we assume that the features we
implement are not in their final form, so writing automated tests for them takes a
back seat. Unit testing UIs is hard; why should we spend time testing unfinished
features when there are NEW features that need implementing?

This leads to codebase entropy (technical debt), because every "just-get-it-done"
feature is an untested feature that may change when we put it in front of users.
We cannot design a consistent application when every feature is developed in a
vacuum without user input. Every usability change we're rushed into making
(because we didn't budget time in the sprint for user feedback) results in a
tangle of code, because the desired behavior was not considered as part of the
initial design.

We're not bad or sloppy developers; we clean up the code even as we implement
new features. The new features we write now will be ones we clean up in the
future; this isn't a bad thing, it's continuous refactoring. Unfortunately, most
continuous refactoring fixes are "quick wins," because anything that could bring
about the BIG wins (such as when we implemented webpack or UI routing) takes
weeks to implement and doesn't result in any new features or cosmetic enhancements,
making it a hard sell to the people writing the checks. Those big wins allowed
us to successfully deliver R9 on an extremely aggressive schedule, but we only
celebrate what we can see, and what we can see still needs improvement.

As our codebase grows, our technical debt compounds, but our productivity
"income" remains constant. We quickly find ourselves in a negative feedback loop
where any forward velocity on new features results in twice as much technical
debt per hour of labor. How do you convince stakeholders that it's necessary to
spend weeks fixing invisible problems when there are very visible problems that
need fixing along with requested features that haven't yet been developed?

As we become a customer-facing organization, the quality of our user interfaces
becomes extremely important. I believe "good enough" is no longer good enough,
and that changes (both cultural and technical) need to be made to how we develop
user interfaces in order to achieve our full potential.

# Clojure(script) is the answer
I have invested months of my free time researching technologies and techniques
in search of answers to the problems outlined above. I've discovered that there
are battle-tested tools and practices (used by other companies whose products and
services we like and respect, such as CircleCI) that could fundamentally improve
the way we build UIs at OpenWhere: Both the process (for developers AND
stakeholders) as well as the end product.

Every solution I've sought for these problems has eventually led back to the
Clojure community. They have treasures are hiding in plain sight for anybody who
can get past the parentheses, and I'm excited by the practices and techniques
enabled by these tools.

What I am proposing will not be simple to implement. Learning new tools, problem-
solving techniques, and ways of thinking about problems is a time-intensive
process, and I understand that time is a resource that is in short supply.

What I hope to convince you of, however, is that the dividend offered by these
new tools and skills will far outweigh the cost of learning them.

In fact, I believe the benefits are so great, they outweigh even the cost
of transitioning our existing products to Clojure(script).

My objective today is *not* to sell you on a rewrite of our codebase.

My objective today is to discuss, in detail, how the Clojure/Clojurescript
ecosystem can vastly improve the quality of life for UI developers and
stakeholers, how it can speed up our UI development process, how it can lead to
tighter, **positive** feedback loops, and how it enables easy (and thorough) unit
testing of the UI.

Hopefully, what you'll take away from this is a willingness to have the hard
conversation about the cost of adopting new tools and practices. Because there
are some real treasures here; I only hope I can do a sufficient job explaining
the benefits.

# Topics
1. State Management and the Digest Cycle
2. Clojure(Script) and the UI
3. Rapid Feedback Loops via Component-first development (Devcards)
  - Forces us to think about data in the beginning
  - Extremely easy edge-case testing
  - Rapid iteration and the ability to "Drop Into" the application
4. Unit testing UI components
  - Devcards may be sufficient
  - Components as functions
  - Randomly generated test data
  - Spec?
5. The ecosystem
  - Clojure for front and back
  - JS interop for libraries like Leaflet
  - Community (& Hammock-Driven Development)
5. The user experience (freebies)
"Everything you love about the Javascript ecosystem, without the Javascript!"
  - Built-in features of the language (lodash, immutable data structures)
  - Google Closure Compiler (webpack++)
  - Leinengen (npm++)
  - Figwheel (live development/auto reload/human debugging)




I have invested months of my free time researching how good UI development can
be, and I believe the answers to our problems lie in the Clojure ecosystem, and
the community surrounding it.

In every HackerNews comments section about a great new Javascript tech are
comments about how Clojure library X is the inspiration behind the new hotness,
or how the solution to problem Y is simpler (or doesn't exist!) in Clojure.

