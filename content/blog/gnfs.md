---
title: "general number field sieve (gnfs)"
summary: GNFS notes
draft: true
---

## Difference of squares factoring method

If you want to factor a number \\( n = pq \\) and you also know \\( s^2 \equiv r^2 \bmod{n} \\), then \\( s^2 - r^2 = (s - r)(s + r) \vert pq \\). This means sometimes \\( p \\) or \\( q \\) will be the gcd of \\( n \\) and \\( s - r \\) or the gcd of \\( n \\) and \\( s + r \\). When that's the case, which has \\( \frac{2}{3} \\) chance, we can factor \\( n \\). When the gcds are only in \\( \\{1, n\\} \\), we get no new information to factor \\( n \\).

## GNFS preconditions

Given a number \\( n \\) to be factored, you must choose an \\( m \\) and a polynomial \\( f \\) such that \\( f(m) = 0 \bmod{n} \\). If you choose an \\( m \\) first where \\( m < n \\), the polynomial \\( f(m) = a_dm^d + a_{d-1}m^{d-1} + ... + a_0 \\) with \\( m \\) parameterized is the generalized base-m representation of n and fits \\( f(m) = 0 \bmod{n} \\).

Let \\( \mathbb{Z}[\theta] = \\{ a_{d-1}\theta^{d-1} + a_{d-2}\theta^{d-2} + ... + a_0 \\} \\) be the ring of complex numbers such that \\( a_i \in \mathbb{Z} \\) and \\( f(\theta) = 0 \\). If \\( a(x), b(x) \\) are two polynomials such that \\( a(\theta) = A, b(\theta) = B\in \mathbb{Z}[\theta] \\), then \\( AB = a(\theta)b(\theta) = e(\theta)f(\theta) + c(\theta) = C\in \mathbb{Z}[\theta] \\). This means when \\( x = \theta \\), we can simplify \\( a(x)b(x) \\) with \\( a(x)b(x) \bmod{f(x)} \\).

## GNFS

If we can find a \\( \beta^2 \in \mathbb{Z}[\theta] \\) and a \\( y^2 \in \mathbb{Z} \\) then it may be possible to factor \\( n \\) with the difference of squares method. Building on top of the foundations of the preconditions, a theorem states that choosing an \\( m \\) and a polynomial \\( f(m) = 0 \bmod{n} \\) with root \\( \theta \\) results in a homomorphism \\( \phi : \mathbb{Z}[\theta] \rightarrow \mathbb{Z}/n\mathbb{Z} \\) satisfying

\\[ \tag{1} \phi(ab) = \phi(a)\phi(b) \\]
\\[ \tag{2} \phi(a+b) = \phi(a) + \phi(b) \\]
\\[ \tag{3} \phi(1) \equiv 1 \bmod{n} \\]
\\[ \tag{4} \phi(\theta) \equiv m \bmod{n} \\]

Then we can lazily reason that \\( s^2 = \phi(\beta^2) \cong \phi(\theta^2) \equiv m^2 \cong r^2 \bmod{n} \\).

## Generating smooth squares in the rationals

Given the set of all B-smooth numbers, or numbers whose prime factors are less than or equal to B, one can find if the multiplication of elements in a subset is a perfect square by checking if all the exponents in the prime factorization of the multiplied number are all even. To find all possible subsets with this property, form a matrix with the exponents of the original set as rows, multiply it with a vector of variables, set it equal to the zero vector in GF(2), and solve for the vector of variables.

## Algebraic number theory background

The previous example of finding squares defined smoothness in the context of a rational (\\( \mathbb{Q} \\)) factor base. In an algebraic factor base, or a subset of \\( \mathbb{Z}[\theta] \\), one must first define its equivalent of a prime. It's called a prime ideal \\( \\{ a+b\theta \\} \\). An element of \\( \mathbb{Z}[\theta] \\), \\( a+b\theta \\), is a prime ideal if there exists no \\( c, d \\) in \\( \mathbb{Z}[\theta] \\) such that \\( cd = a + b\theta \\). A smooth number with a algebraic factor base will be the multiplication of some prime ideals.

However, programming prime ideals and algebraic factor bases requires more data modelling. Luckily, there is an isomorphism from prime ideals to the integers. There's a set of pairs \\( \\{(r, p)\\} \\) where p is prime and \\( f(r) \equiv 0 \bmod{p} \\) that forms a bijection with the set of \\( a + b\theta \in \mathbb{Z}[\theta] \\).

## Algebraic number theory theorems

T1. If \\( c + d\theta = (r, p) \\), \\( c + d\theta \vert a + b\theta \\) iff \\( a \equiv -br \bmod{p} \\).

T2. A set of pairs \\( (r, p) \in \mathbb{Z}[\theta] \\) represents a factorization of \\( a + b\theta \\) iff \\( \prod_{(r_i, p_i)} p_i = (-b)^df(-a/b) \\).

T3. A prime number \\( q \\) divides \\( a + bm \\) iff \\( a \equiv -bm \bmod{q} \\).

## Smooth number generator algorithm

Trying to build a database of smooth numbers with both rational and algebraic bases, one must find integer pairs \\( (a, b) \\) such that \\( a + bm \\) is smooth in the rationals and \\( a + b\theta \\) is smooth in the algebraic.

1. Find a random \\( b \in \mathbb{Z} \\) and \\( N \in \mathbb{Z}_{> 0} \\).
2. Let \\( a \\) vary from \\( -N \\) to \\( N \\) and create two arrays for the resulting \\( a + bm \\) and \\( a + b\theta \\).
3. For every rational factor \\( q_i \\) in rational factor base \\( R \\), plug \\( q_i \\) in T3 and cache every \\( a \\) that makes T3 affirmative.
4. For every algebraic factor \\( (r_i, p_i) \\) in algebraic factor base \\( A \\), find every \\( a \\) that makes T1 affirmative. Then, for every \\( a \\) with complete pairs \\( (r_i, p_i) \\) that make T2 affirmative, cache those \\( a \\)s.
5. Locate the intersection of \\( a \\)s in 3 and 4.
6. Repeat for a different fixed \\( b \\).

## Sources

1. [A Beginner's Guide to the GNFS](https://www.cs.umd.edu/~gasarch/TOPICS/factoring/NFSmadeeasy.pdf)
