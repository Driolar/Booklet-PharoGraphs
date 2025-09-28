## Matchings (Independent Edge Sets)
In graph theory, a matching or independent edge set in an undirected graph is a set of edges without common vertices. 
The term matching is the more popular one, but it shouldn't be confused with another meaning of graph matching, namely computing the similarity of graphs (graph isomorphism).

Matchings are used in various applications such as network design, job assignments, and scheduling. 
More specifically, matching strategies are very useful in flow network algorithms such as the Edmonds-Karp algorithm that is also in our library.

### Some definitions and small examples
The **cardinality** of a matching is the number of its edges. Figure @maximal@ shows 3 matchings of cardinality 1, 2 and 2 (from left to right).

![Matchings.](figures/Maximal-matching.pdf width=50&label=maximal)

A matching is **maximal** if it cannot be expanded to another matching by addition of any edge in the graph.
The matchings in figure @maximal@ are maximal.

Moreover, a **maximum**(-cardinality) matching has the largest possible cardinality among all matchings in a graph.
For each of the matchings in figure @maximal@, figure @maximum@ shows a maximum matching example with cardinality 2, 3 and 2 respectively.

![Matchings.](figures/Maximum-matching.pdf width=50&label=maximum)

We note from these examples that a maximum matching is always maximal, but the converse does not always hold.

A **maximum-weight** matching in a weighted graph is a matching with the largest sum of weights. 
Similarly, a **minimum-weight** matching has the smallest sum of weights.

### A greedy algorithm for matching
The algorithm for graph matching implemented in this library is a simple greedy algorithm with time complexity of $O(|E|log(|V|))$.
The class ```AIGraphMatchingAlgorithm``` can be instantiated to find an **approximation** either of the maximum-weight, the minimum-weight or the maximum-cardinality matching.

For instance, the pseudocode to greedily find a matching of large weight is the following one:
```
M ← Empty list that will contain the matching edges
E ← Set of all edges
remove all loops (edges joining a vertex to itself) from E

while E is not empty do
    let e be the edge in E with the highest weight
    add e to M
    remove e and all edges incident to e from E
```
For finding a matching of small weight, we use exactly the same algorithm except that we have to order the edges in descending weight instead of ascending.
This is the same principle we already met for the Kruskal's algorithm.

In any case, the greedy graph matching algorithm finds a maximal matching but it doesn't always find the optimal solution, in contrast to more expensive matching algorithms like the Hungarian Maximum Matching Algorithm, the Blossom Algorithm or the Hopcroft–Karp Algorithm.
Nevertheless, it can be proven that it is a 2-approximation (greedy result >= 1/2 optimal result).

To see that the result is not always optimal, consider the two matching versions of the weighted graph with four nodes and three edges in figure @epsilon@.

![Matchings.](figures/epsilon.pdf width=50&label=epsilon)

The greedy algorithm for maximum-weight would first take weight 1+epsilon and then stop (figure @epsilon@ top), missing the optimal weight sum 1+1 (figure @epsilon@ bottom).
Note, verifying the 2-approximation, that if the weight 1+epsilon would be 2 instead, the greedy algorithm would yield one of the maximum-weight matchings.

### Stable matchings
Maximality and minimality are not the only interesting optimal matching problems.
Another goal for optimal matching is stability, based on mutual preferences between two contender groups such as men and women or students and colleges.

Our library implements the classical Gale-Shapely algorithm that solves the important Stable Matching Problem (aka Stable Marriage Problem).
To simplify, the problem considers two groups A and B of equal size n, modelled as a complete **bipartite** graph.
Each group member has defined a strict preference ordering over all the members of the other group.
A resulting matching would contain n edges, each one relating a different pair.

A matching is **stable** when there does not exist any pair which both prefer each other to their current partner under the matching.
This leads to a sense of harmony and fairness in the outcome. 

Here is the algorithm's pseudocode:
```
Every vertex begins unmatched.
While there is an unmatched vertex in the set A
    the next unmatched vertex in A proposes to match with its most preferred B vertex it has not already tried to match with
        if the B vertex was unmatched then these vertices are now matched
        if the B vertex was matched, it chooses its preferred vertex among the proposed one or the existing match;
            depending on the choice, the proposed match wins or vertex A remains unmatched
```
This algorithm with complexity $O(n²)$ finds in any case a stable matching. However, it is extreme and dual in the sense that it generally yields the matching that is best for group A among all stable matchings, and worst (but still stable) for group B.

To run ```AIStableMatchingAlgorithm```, both groups of equal size must be set with the complete preferences of each contender ```AIStableMatchingNode``` in order to obtain the stable matching, a set of ```AIStableMatchingEdge```. 

The Stable Matching Problem has extensions in many ways, e.g. with groups of different size or incomplete preference lists. 
### Conclusion
Graph matching is a challenging problem, especially in large and complex graphs where scalability and complexity are crucial.
Many graph matching algorithms exist in order to optimize for the parameters necessary dictated by the problem at hand.
In such contexts, approximation algorithms may be suitable.

From the practical and theoretical points of view, envisaging the special case of bipartite graphs is very common and fructiferous, notably for matching problems.

Both the greedy and the stable matching algorithm presented here are further examples of using a working list until emptied, like discussed for the topological sorting.
