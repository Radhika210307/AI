# AI

**1. Family tree and relationships**
```prolog
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
```


?- father(john, Who).      % Who = paul ; Who = linda.
?- grandparent(mary, Who). % Who = mike ; Who = david (depending on facts).
?- uncle(Who, mike).       % Who = linda's brothers if any etc.
?- ancestor(john, david).  % true.


---

2. conc(L1, L2, L3) — append
```prolog
conc([], L, L).
conc([H|T], L2, [H|R]) :-
    conc(T, L2, R).
```

Example:

?- conc([1,2], [3,4], R). % R = [1,2,3,4].
?- conc(A, B, [1,2]).     % generates splits of [1,2].


---

---

3. reverse(L, R)

Two versions: simple (uses conc) and efficient accumulator.

Efficient:
```prolog

reverse(L, R) :- rev_acc(L, [], R).

rev_acc([], Acc, Acc).
rev_acc([H|T], Acc, R) :-
    rev_acc(T, [H|Acc], R).
```

Example:

?- reverse([1,2,3], R). % R = [3,2,1].


---

4. sum of two numbers

```
sum2(X, Y, S) :-
    S is X + Y.
```
Example:

?- sum2(7, 5, S). % S = 12.


---

5. max(X, Y, M)
```
max(X, Y, X) :- X >= Y, !.
max(_, Y, Y).
```
Example:

?- max(5, 9, M). % M = 9.
?- max(10, 2, M). % M = 10.


---

6. factorial(N, F)
```
factorial(0, 1) :- !.
factorial(N, F) :-
    N > 0,
    N1 is N - 1,
    factorial(N1, F1),
    F is N * F1.
```
Example:

?- factorial(5, F). % F = 120.


---

7. generate_fib(N, T) — Nth Fibonacci term

(0-based: fib(0)=0, fib(1)=1)
```
generate_fib(0, 0) :- !.
generate_fib(1, 1) :- !.
generate_fib(N, T) :-
    N > 1,
    N1 is N - 1, N2 is N - 2,
    generate_fib(N1, T1),
    generate_fib(N2, T2),
    T is T1 + T2.

Note: recursive version is exponential; for large N use iterative/accumulator version.

Iterative (efficient):

generate_fib_iter(N, T) :-
    fib_acc(N, 0, 1, T).

fib_acc(0, A, _, A) :- !.
fib_acc(N, A, B, T) :-
    N > 0,
    N1 is N - 1,
    C is A + B,
    fib_acc(N1, B, C, T).
```
Examples:

?- generate_fib(6, T).       % T = 8 (0,1,1,2,3,5,8).
?- generate_fib_iter(20, T). % fast.


---

8. power(Num, Pow, Ans) — integer nonnegative Pow
```
power(_, 0, 1) :- !.
power(Num, Pow, Ans) :-
    Pow > 0,
    Pow1 is Pow - 1,
    power(Num, Pow1, A1),
    Ans is A1 * Num.
```
(For negative exponents you'd use floats and reciprocals.)

Example:

?- power(2, 8, A). % A = 256.


---

9. multi(N1, N2, R) — multiplication
```
multi(N1, N2, R) :-
    R is N1 * N2.
```
Example:

?- multi(7, 6, R). % R = 42.


---

10. memb(X, L) — membership
```
memb(X, [X|_]) :- !.
memb(X, [_|T]) :-
    memb(X, T).
```
(Or use built-in member/2.)

Example:

?- memb(3, [1,2,3,4]). % true.
?- memb(a, [b,c]).     % false.


---

11. sumlist(L, S) — sum of elements
```
sumlist([], 0).
sumlist([H|T], S) :-
    sumlist(T, S1),
    S is H + S1.
```
Example:

?- sumlist([1,2,3,4], S). % S = 10.


---

12. evenlength(List) and oddlength(List)

Method: remove two elements for even; odd if one element remains.
```
evenlength([]).
evenlength([_,_|T]) :-
    evenlength(T).

oddlength([_]).
oddlength([_,_|T]) :-
    oddlength(T).
```
Examples:

?- evenlength([a,b,c,d]). % true.
?- oddlength([a,b,c]).    % true.
?- evenlength([]).        % true.


---

13. maxlist(L, M) — maximum in list
```
maxlist([H], H) :- !.
maxlist([H|T], M) :-
    maxlist(T, M1),
    ( H >= M1 -> M = H ; M = M1 ).
```
Example:

?- maxlist([3,7,2,9,5], M). % M = 9.


---

14. insert(I, N, L, R) — insert I at position N (1-based)
```
insert(I, 1, L, [I|L]) :- !.
insert(I, N, [H|T], [H|R]) :-
    N > 1,
    N1 is N - 1,
    insert(I, N1, T, R).
% if position is beyond end, will fail; to append at end, you can add clause.
```
Example:

?- insert(x, 2, [a,b,c], R). % R = [a,x,b,c].
?- insert(z, 1, [a,b], R).   % R = [z,a,b].

If you want safe append when N > length+1, add an extra clause to append.


---

15. delete(N, L, R) — remove element at position N (1-based)
```
delete(1, [_|T], T) :- !.
delete(N, [H|T], [H|R]) :-
    N > 1,
    N1 is N - 1,
    delete(N1, T, R).
```
Example:

?- delete(2, [a,b,c,d], R). % R = [a,c,d].
?- delete(1, [x,y], R).     % R = [y].


---
