---
layout: post
title: Continuations and Effects
tags: continuations
description: continuations and effects
---
# Relationship between monads and continuations
mother of all monads. Buyt also Filinkski paper. refiication and reflection - In a langue with  effects like ocaml, we can reflect / reify the exceptions or mutation into their pure form that uses monad dsiciplaine. Converting native ocaml exception to Either exceptions [https://www.reddit.com/r/haskell/comments/1cyajx/the_mother_of_all_monads_school_of_haskell/](https://www.reddit.com/r/haskell/comments/1cyajx/the_mother_of_all_monads_school_of_haskell/) some caustic yet intriguing discussion. The embedding is (x >>=) like how cps embedding is (x $)?

[https://gist.github.com/sjoerdvisscher/a56a286ccfabce40e424](https://gist.github.com/sjoerdvisscher/a56a286ccfabce40e424) This is interesting. Using cont monad in swift probably to deal with the lack of higher kinded types. Similar to rust problem

https://www.reddit.com/r/haskell/comments/1cyajx/the_mother_of_all_monads_school_of_haskell/

# Delimitted Continuations
Oleg delimcc an implemntation of delimitted continuations for the

# Exceptions

# Call-cc
call with current continuation
Relationship to double negation encoding

# Algebraic effects

What even are algebraic effects?

[Ocaml is getting an effect system](https://pldi21.sigplan.org/details/pldi-2021-papers/14/Retrofitting-Effect-Handlers-onto-OCaml) 
See my ocaml notes for more.

* https://www.stephendiehl.com/posts/exotic03.html effect systems are off to the side, but do they help explain lifetimes?  https://news.ycombinator.com/item?id=25178437 interesting commments. Oleg talk. Frank language
* divergence as an effect. But also is memory usage an effect 
* ocaml algebraic effects.  https://github.com/ocaml-multicore/effects-examples https://www.youtube.com/watch?v=z8SI7WBtlcA&feature=emb_title&ab_channel=JaneStreet https://ocamlverse.github.io/content/future_ocaml.html#typed-algebraic-effects https://github.com/ocamllabs/ocaml-effects-tutorial
* There was an andrej bauer video https://www.youtube.com/watch?v=atYp386EGo8&ab_channel=OPLSS
*  Sandy Maguire and polysemy
* resumable exceptions.
* Related the the python yield stuff. 
* Daan Leijen paper https://www.microsoft.com/en-us/research/wp-content/uploads/2016/08/algeff-tr-2016-v2.pdf comes up
* Koka, Eff, F*, Link, Helium
* Plotkin papers
* Alexis King effects for less https://www.youtube.com/watch?v=0jI-AlWEwYI&ab_channel=Z%C3%BCrichFriendsofHaskell
* https://github.com/ghc-proposals/ghc-proposals/pull/313 delimitted continuation primops ghc proposal. Lots of interestnig discussion
* Pretnar 2015  An Introduction to Algebraic Effects and Handlers Invited tutorial pap https://www.eff-lang.org/handlers-tutorial.pdf
- https://cs.ru.nl/~dfrumin/notes/delim.html delimitted contiunuations
- Asai
- [Eff directly in ocaml](https://arxiv.org/pdf/1812.11664.pdf) Kiselyov Sivaramakrishnan  


# Resources
[https://github.com/rain-1/continuations-study-group/wiki/Reading-List](https://github.com/rain-1/continuations-study-group/wiki/Reading-List)

Coroutines and generators as effect systems. javascript cotuotines to conitations [https://gist.github.com/yelouafi/bbc559aef92f00d9682b8d0531a36503](https://gist.github.com/yelouafi/bbc559aef92f00d9682b8d0531a36503)

Rebvisting coroutines [http://www.inf.puc-rio.br/~roberto/docs/MCC15-04.pdf](http://www.inf.puc-rio.br/~roberto/docs/MCC15-04.pdf)

[https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.398.9600&rep=rep1&type=pdf](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.398.9600&rep=rep1&type=pdf) Yield - mainstream delimitted continuations. slides [http://parametricity.net/dropbox/yield-slides.subc.pdf](http://parametricity.net/dropbox/yield-slides.subc.pdf)

https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.399.1021&rep=rep1&type=pdf
Lazy v Yield - kiselyov sabry peytonjones
Inremental linear pretty printing

https://twitter.com/sigfpe/status/1274764185658769408?s=20

io.py [https://gist.github.com/alexknvl/e15ea9762a58935b28f2](https://gist.github.com/alexknvl/e15ea9762a58935b28f2)
https://github.com/alexknvl/ai-meetup/blob/master/19-09-26-ppl1/ppl-meetup.ipynb probablistic programming
https://twitter.com/ezyang/status/1300278419188580353?s=20

[https://pypi.org/project/effect/#:~:text=Effect%20is%20a%20library%20for,It%20supports%203.6%20and%20above.](https://pypi.org/project/effect/#:~:text=Effect%20is%20a%20library%20for,It%20supports%203.6%20and%20above.) effect - as python library

takafumi asrakaki using julia yieldto for a "callcc"
https://julialang.zulipchat.com/#narrow/stream/137791-general/topic/call.2Fcc.20for.20Julia.3F.20%28or.20how.20to.20use.20.60yieldto.60%29/near/218188425


[https://news.ycombinator.com/item?id=24257488](https://news.ycombinator.com/item?id=24257488) Anatomy of Lisp. Lisp in small pieces.

[https://www.cs.utexas.edu/ftp/garbage/cs345/schintro-v14/schintro_142.html#SEC271](https://www.cs.utexas.edu/ftp/garbage/cs345/schintro-v14/schintro_142.html#SEC271)

Lambda prolog book is drectly talking about semantics in terms of sequents

[https://github.com/nornagon/jonesforth/blob/master/jonesforth.S](https://github.com/nornagon/jonesforth/blob/master/jonesforth.S) Jones forth tutorail assembly

https://slang.soe.ucsc.edu/cormac/papers/pldi93.pdf The essence of compiling with continuations. Pushing A normal form.