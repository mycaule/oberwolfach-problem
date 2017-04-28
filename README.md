# Oberwolfach problem experiments
Experimenting on the Oberwolfach problem

#### If `N=10` people are in a ballroom are inviting each other `K=2` to dance, how many rounds `R` will they need to dance with everyone ?

Given people in the `List(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)`. We pair them together for several rounds so that everybody meets everyone in the list.

```scala
val l0 = List.range(0,10)
> List(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)

val l1 = l0.filter(_ % 2 == 0) // even numbers
val l2 = l0.filter(_ % 2 == 1) // odd numbers
l1.zip(l2)

> List((0,1), (2,3), (4,5), (6,7), (8,9))
```

Switching element at index 1 with index 2
```scala
val people = l0.updated(1,l0(2)).updated(2,l0(1))

val males = l0p.zipWithIndex.filter(_._2 % 2 == 0).map(_._1)
val females = l0p.zipWithIndex.filter(_._2 % 2 == 1).map(_._1)
l1p.zip(l2p)

> List((0,2), (1,3), (4,5), (6,7), (8,9),)
```

We then generalize on element #0 and considering the pairs as sets working on the sequence `x_i,0 = swap(1, i, people)`

```scala
https://scalafiddle.io/sf/EeY4Ujr/0
val people = List.range(0,10)

def swap(i: Int, j: Int, l: Seq[Int]) = {  
  l.updated(i,l(j)).updated(j,l(i))
}

def moduloIndex(p: Int, i: Int, l: Seq[Int]) = {
  l.zipWithIndex.filter(_._2 % p == i).map(_._1)
}

def pairUp(l: Seq[Int]) = {
  moduloIndex(2, 0, l).zip(moduloIndex(2, 1, l))
  .map {
    x => if (x._1 < x._2) (x._1, x._2) else (x._2, x._1)
  }
}

for(i<-people.tail) println(pairUp(swap(1, i, people)))

> List((0,1), (2,3), (4,5), (6,7), (8,9))
> List((0,2), (1,3), (4,5), (6,7), (8,9))
> List((0,3), (1,2), (4,5), (6,7), (8,9))
> List((0,4), (2,3), (1,5), (6,7), (8,9))
> List((0,5), (2,3), (1,4), (6,7), (8,9))
> List((0,6), (2,3), (4,5), (1,7), (8,9))
> List((0,7), (2,3), (4,5), (1,6), (8,9))
> List((0,8), (2,3), (4,5), (6,7), (1,9))
> List((0,9), (2,3), (4,5), (6,7), (1,8))
```

At this point `R` is at least `N`, #1 has already danced with #0, #2, sometimes couple of times, but still we have to enlist all remaining choices for element #1. Working on `x_i,1 = swap(1, i, people.drop(1))` now gives
```scala
https://scalafiddle.io/sf/EeY4Ujr/2

for(i<-people.drop(2)) println(pairUp(swap(1, i, people.drop(1))))
> List((1,3), (2,4), (5,6), (7,8))  // ++ (0,9)
> List((1,4), (2,3), (5,6), (7,8))  // ++ (0,9)
> List((1,5), (3,4), (2,6), (7,8))  // ++ (0,9)
> List((1,6), (3,4), (2,5), (7,8))  // ++ (0,9)
> List((1,7), (3,4), (5,6), (2,8))  // ++ (0,9)
> List((1,8), (3,4), (5,6), (2,7))  // ++ (0,9)
> List((1,9), (3,4), (5,6), (7,8))  // ++ (0,2)
```

Considering then `x_i<j = swap(i, j, people)`

How do we know we don't have duplicates ? Verify this computationally!

#### General formula for number of rounds `R = f(N, K)`

```
R <= card(x_i,0) + card(x_i,1) + ... + card(x_i,N-1)
  <=    (N-1)    +     (N-2)   + ... + 0
  <=  N x (N-1)/2
```
