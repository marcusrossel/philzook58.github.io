

https://categorytheory.zulipchat.com/#narrow/stream/229136-theory.3A-category.20theory/topic/Software.20packages.20for.20computer-assisted.20category.20theory/near/247080854
Discussion of interesting stuff Anne Heyworth thesis rewriting for kan extenbsion https://arxiv.org/abs/math/9812097


https://www.cs.cornell.edu/~kozen/Papers/06ijcar-categories.pdf Dexter Kozen nuprl.
We should revisist this.

Could we make a theory of functors?



Problems to pose:
Finitely presented categories
Axiomatic categories - (equality questions over)
pullback of monic is monic
demonstrate universal properties or reason with them. There exists unique is a problem.




Look at category theory textbook to collect problems?
Higher falutin stuff. Adjunction reasoning Functor. Expressing the yoneda lemma.




Verify that concrete mathematical objects obey categroical axioms
Prove that any object that obeys categorical axiomns
In this case we have construction X, vs we always have constructin X.

Look at Isabelle. How automated are Isabelle category packages
TPTP encodings


Graphs vs terms


ATP - 
SMT - 
??? - Categorical automation


Automation takes a strong first order logic flavor.


EGraph techniques - EGraphs have risen in prominence recently thanks the egg paper and implementation, but the core idea is quite old.


Knuth Bendix
Operad book


Biting off obviously manageable chunks - Extremely constrained
Finite categories
Finitely presented categories



Fabirizio whatever's company
Ryan wisknesky's company
That python category thing
GAP categories



Given f : A -> B and g : B -> A such that f.g = 1 and g.f = 1, show that 
any other such g' must equal g.

f.g = id(A)
g.f = id(B)
f.g2 = id(A)
g2 . f = id(B)

prove
g1 = g2

That's good. This is the analog of uniqueness of identity in group theory

Hmmmm... What if I took my yoneda lemma technique and used it in Vampire?
Typed terms.
What about a prove with intrinsic polymorphism support
https://link.springer.com/chapter/10.1007/978-3-030-51054-1_21


f g m m'

fof(  ![X]: f(g(X)) = m(g(X)) ) % square commutes
fof(     ) # pullabck condition
We need to qwuantify over morphisms, which FOL will not let us do.
Howveer, zipperposition offers lambda free HOL?




WWhat about automating co end calculus profunctor nonsense.
Hom(-,-)
????

could I use the standard rewrites of categories to develop something...
Equivalence proofs?
(a * b) + c etc. ordinary algebra.

adjunctions
a -> G b  <=> F a -> b 
kan extensions

# Category theory of sets ETCS

https://arxiv.org/pdf/1212.6543.pdf Leinster. great paper

Topos in elementary terms

pullback = substitions https://math.stackexchange.com/questions/2403265/substitution-is-pullback

Pullback takes in 3 objects, 2 morphisms, and outputs object, 2 morphisms, and the universal morphism generators.

Sierpinski as truth object

What about doing my category theory thing with computable functions. Sierp is truth object?

Gneralized points - elements are functions {.} -> a
Subsets = monomorphisms
function application = function composition
Points = terminal object domained morphism (pointed?)


what are pullbacks?
Solutions to Ax=By
inverse images Ax = vl , if B is a vector, that is the analog of a point prjectively, so this
calculated the subspace that maps to that point

Cody said somoehow the bottom one can be subsutition and the side one
