# The first law of complexodynamics

Type: Blog
Link: https://scottaaronson.blog/?p=762

**Question: Why does the sophistication/interestingness of some physical systems increase with time, reach a maximum, and then decrease, while entropy always increases?**

![Untitled](The%20first%20law%20of%20complexodynamics%2005b3e3a999024d8a8650400cf3dbc71f/Untitled.jpeg)

Evolution of this complexity (= Complexity of the system):

- Early: Low entropy -> (small) Upper bound on the complexity.
- Middle: What can we say about the middle part: Low complexity? Large complexity? How Large/small? What kind of complexity is it? Is there any predictability in the system? These are the questions.
- Late: System has reached an equilibrium -> Limited (complexity) by definition

The goal of this blog is to study the “middle” part using sophistication from Kolmogorov complexity:

- Entropy defined with Kolmogorov complexity:
    - Deterministic system: Entropy increases in log(t). If probabilistic systems: polynomial. For both Kolmogorov complexity (length of the shortest computer program that outputs °) and resource-bounded Kolmogorov complexity (same but in time of the program instead of length)
- Complexity defined with Kolmogorov complexity:
    - (Def) sophistication of x is the length of the shortest computer program that describes, not necessarily x itself, but a set S of which x is a “random” or “generic” member (in other words, the structured/interesting complexity of a string to avoid to simple/random strings).
    - With this sophistication, the complexity never exceeds log(t)+c
        - Simple system: complexity doesn’t exceed log(t) + c because bounded by init state, transition rules, and number of steps.
        - Probabilistic systems: specify the set of all possible states after t time steps, S(t), after the init state (also specified), and then by a counting argument (I.e. all states are equally likely to happen, so doesn’t require much bits).
    - Thus, the current definition of sophistication doesn’t capture complex strings as we want. The author introduces another definition of sophistication using computational resource bounds (and introduces *complextropy*). That new definition solves the problem because: there is no short program to generate specific arrangements, and to sample that string would need in any case a program log2(|S|)-c bits.

In short: how to describe the complexity of a system, as we might think about it (e.g., coffee with milk).

References:

- From Eternity to Here: The Quest for the Ultimate Theory of Time, S. Carroll, 2010
- [https://en.wikipedia.org/wiki/Kolmogorov_complexity](https://en.wikipedia.org/wiki/Kolmogorov_complexity)
- Proof that sophisticated strings exist: Algorithmic Statistics, P. Gacs, 2001