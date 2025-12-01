# AI

**1. Family tree and relationships**
% sample facts
male(john).
male(paul).
male(mike).
male(david).

female(mary).
female(linda).
female(susan).

parent(john, paul).   % john is parent of paul
parent(mary, paul).
parent(john, linda).
parent(mary, linda).
parent(paul, mike).
parent(susan, mike).
parent(linda, david).

% basic relations
father(X,Y) :- male(X), parent(X,Y).
mother(X,Y) :- female(X), parent(X,Y).
grandparent(X,Y) :- parent(X,Z), parent(Z,Y).
grandfather(X,Y) :- male(X), grandparent(X,Y).
grandmother(X,Y) :- female(X), grandparent(X,Y).

sibling(X,Y) :- parent(P,X), parent(P,Y), X \= Y.
brother(X,Y) :- male(X), sibling(X,Y).
sister(X,Y) :- female(X), sibling(X,Y).

uncle(X,Y) :- male(X), parent(P,Y), sibling(X,P).
aunt(X,Y)  :- female(X), parent(P,Y), sibling(X,P).

cousin(X,Y) :-
    parent(Px,X), parent(Py,Y),
    sibling(Px,Py),
    X \= Y.

ancestor(X,Y) :- parent(X,Y).
ancestor(X,Y) :- parent(X,Z), ancestor(Z,Y).



?- father(john, Who).      % Who = paul ; Who = linda.
?- grandparent(mary, Who). % Who = mike ; Who = david (depending on facts).
?- uncle(Who, mike).       % Who = linda's brothers if any etc.
?- ancestor(john, david).  % true.


---

2. conc(L1, L2, L3) — append

conc([], L, L).
conc([H|T], L2, [H|R]) :-
    conc(T, L2, R).

Example:

?- conc([1,2], [3,4], R). % R = [1,2,3,4].
?- conc(A, B, [1,2]).     % generates splits of [1,2].


---
