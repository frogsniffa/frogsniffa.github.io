---
title: "general number field sieve (gnfs)"
summary: GNFS notes
draft: false
---

## Difference of squares factoring method

If you want to factor a number \\( n = pq \\) and you also know \\( s^2 \equiv r^2 \bmod{n} \\), then \\( s^2 - r^2 = (s - r)(s + r) \vert pq \\). This means sometimes \\( p \\) or \\( q \\) will be the gcd of \\( n \\) and \\( s - r \\) or the gcd of \\( n \\) and \\( s + r \\). When that's the case, which has \\( \frac{2}{3} \\) chance, we can factor \\( n \\). When the gcds are only in \\( \\{1, n\\} \\), we get no new information to factor \\( n \\).

## GNFS preconditions

Given a number \\( n \\) to be factored, you must choose an \\( m \\) and a polynomial \\( f \\) such that \\( f(m) = 0 \bmod{n} \\). If you choose an \\( m \\) first where \\( m < n \\), the polynomial \\( f(m) = a_dm^d + a_{d-1}m^{d-1} + ... + a_0 \\) with \\( m \\) parameterized is the generalized base-m representation of n and fits \\( f(m) = 0 \bmod{n} \\).

Let \\( \mathbb{Z}[\theta] = \\{ a_{d-1}\theta^{d-1} + a_{d-2}\theta^{d-2} + ... + a_0 \\} \\) be a field extension of integers with potential complex root \\( \theta \\) such that \\( a_i \in \mathbb{Z} \\) and \\( f(\theta) = 0 \\). If \\( a(x), b(x) \\) are two polynomials such that \\( a(\theta) = A, b(\theta) = B\in \mathbb{Z}[\theta] \\), then \\( AB = a(\theta)b(\theta) = e(\theta)f(\theta) + c(\theta) = C\in \mathbb{Z}[\theta] \\). This means when \\( x = \theta \\), we can simplify \\( a(x)b(x) \\) with \\( a(x)b(x) \bmod{f(x)} \\).

## GNFS in terms of difference of squares

If we can find a \\( \beta^2 \in \mathbb{Z}[\theta] \\) and a \\( y^2 \in \mathbb{Z} \\) then it may be possible to factor \\( n \\) with the difference of squares method. Building on top of the foundations of the preconditions, a theorem states that choosing an \\( m \\) and a polynomial \\( f(m) = 0 \bmod{n} \\) with root \\( \theta \\) results in a morphism \\( \phi : \mathbb{Z}[\theta] \rightarrow \mathbb{Z}/n\mathbb{Z} \\) satisfying

\\[ \tag{1} \phi(ab) = \phi(a)\phi(b) \\]
\\[ \tag{2} \phi(a+b) = \phi(a) + \phi(b) \\]
\\[ \tag{3} \phi(1) \equiv 1 \bmod{n} \\]
\\[ \tag{4} \phi(\theta) \equiv m \bmod{n} \\]

Then we can lazily reason that \\( s^2 = \phi(\beta^2) \cong \phi(\theta^2) \equiv m^2 \cong r^2 \bmod{n} \\).

## Generating smooth squares in the rationals

Given the set of all B-smooth numbers, or numbers whose prime factors are less than or equal to B, one can find if the multiplication of elements in a subset is a perfect square by checking if all the exponents in the prime factorization of the multiplied number are all even. To find all possible subsets with this property, form a matrix with the exponents of the original set as rows, multiply it with a vector of variables, set it equal to the zero vector in GF(2), and solve for the vector of variables.

## Algebraic number theory background

The previous example of finding squares defined smoothness in the context of a rational (\\( \mathbb{Q} \\)) factor base. In an algebraic factor base, or a subset of \\( \mathbb{Z}[\theta] \\), one must first define its equivalent of a prime. It's called a prime ideal \\( \\{ a+b\theta \\} \\). An element of \\( \mathbb{Z}[\theta] \\), \\( a+b\theta \\), is a prime ideal if there exists no \\( c, d \\) in \\( \mathbb{Z}[\theta] \\) such that \\( cd = a + b\theta \\). A smooth number with a algebraic factor base will be the multiplication of some prime ideals.

However, programming prime ideals and algebraic factor bases requires more data modelling. Luckily, there is an isomorphism from prime ideals to the integers.

## Algebraic number theory theorems

T0. There's a set of pairs \\( \\{(r, p)\\} \\) where p is prime and \\( f(r) \equiv 0 \bmod{p} \\) that forms a bijection with the set of \\( a + b\theta \in \mathbb{Z}[\theta] \\).

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

## Quadratic residue theorems

T4. Let \\( l \in Z[\theta] \\) have a factorization \\( (a_1 + b_1\theta)^{e_1} (a_2 + b_2\theta)^{e_2} ... \\) where \\( a_j + b_j\theta \\) is a factor base. If all \\( e_j \equiv 0 \bmod{2} \\) then \\( l \\) is a perfect square.

Definition. The Legendre Symbol \\( \left(\dfrac{a}{p}\right) \\) where \\( a \in \mathbb{Z} \\) and \\( p \\) is a prime is
1. \\( 1 \\) if \\( x^2 \equiv a \bmod{p} \\) has a solution
2. \\( -1 \\) if \\( x^2 \equiv a \bmod{p} \\) has no solution
3. \\( 0 \\) if \\( p \vert a \\)

T5. Let \\( l \in Z[\theta] = (a_1 + b_1\theta)^{e_1} (a_2 + b_2\theta)^{e_2} ... \\) be a perfect square. Then for any \\( (s, q) \\) from T0 where \\( (s, q) \nmid a_j + b_j\theta \\), \\( \prod_{(a_j, b_j)} \left(\dfrac{a + bs}{q}\right) = 1 \\).

## Testing whether a field extension element is a square

T4 and T5 show what squares look like in the algebraic. We use them in conjunction as a heuristic for a probable square. In T5, we'd choose the set of \\( (s, q) \\) such that at least one corresponds to every prime ideal. This set is called the quadratic character base and every element of this set is the quadratic character. To increase the probability of the heuristic being correct, increase the quadratic character base size.

## Finding smooth and square numbers

Let \\( U \\) be the set of all \\( (a, b) \\) such that \\( a + bm \\) is smooth in a rational factor base \\( R \\) and \\( a + b\theta \\) is smooth in an algebraic factor base \\( A \\). Let \\( R \\) be \\( \\{ t_1, t_2, ..., t_k \\} \\). Let \\( A \\) be \\( \\{ (r_1, p_1), (r_2, p_2), ... (r_l, p_l) \\} \\). Let a quadratic character base \\( Q \\) be \\( \\{ (s_1, q_1), (s_2, q_2), ... (s_u, q_u) \\} \\). For a given \\( (a, b) \\), \\( a + bm \\) has a factorization \\( t_1^{e_1}t_2^{e_2}...t_k^{e_k} \\). \\( a + b\theta \\) has the factorization that can be represented as \\( (r_1, p_1)^{f_1}(r_2, p_2)^{f_2}...(r_l, p_l)^{f_l} \\).

The resulting row vector represented by \\( (a, b) \\) is characterized with some boolean conditions

\\[
    \begin{bmatrix}
        a+bm < 0 \\\
        e_1 \bmod{2} \\\
        ... \\\
        e_k \bmod{2} \\\
        f_1 \bmod{2} \\\
        ... \\\
        f_l \bmod{2} \\\
        \left(\dfrac{a + bs_1}{q_1}\right) \neq 1 \\\
        ... \\\
        \left(\dfrac{a + bs_u}{q_u}\right) \neq 1
    \end{bmatrix}^T
\\]

We can determine whether \\( V \subset U \\) results in perfect square representations in \\( R \\) and \\( A \\) if, after multiplying the U matrix (repetition of row vector by size U) with a column vector representing the subset \\( V \\), the resulting vector is the zero vector.

## GNFS algorithm

1. Choose an \\( m \in \mathbb{Z} \\) and compute an \\( f \\) via base-m.
2. Define rational factor base \\( R \\) with cardinality \\( k \\).
3. Define algebraic factor base \\( A \\) with cardinality \\( l \\).
4. Define quadratic character base \\( Q \\) with cardinality \\( u \\). Make sure\\( A \\) and \\( Q \\) don't overlap because they have the same definitions.
5. For fixed \\( b \in \mathbb{Z} \\) locate the array of \\( a \\)s such that they're smooth in the rational and algebraic fields. Repeat for more \\( b \\)s until you get more than \\( 1 + k + l + u \\) pairs.
6. Create the \\( U \\) matrix.
7. Solve for \\( V \\).
8. Map the algebraic to the rationals via \\( \phi \\) and factor using difference of squares.

## Sources

1. [A Beginner's Guide to the GNFS](https://www.cs.umd.edu/~gasarch/TOPICS/factoring/NFSmadeeasy.pdf)
