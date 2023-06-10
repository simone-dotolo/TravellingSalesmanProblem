# Travelling Salesman Problem (TSP)

The **Travelling Salesman Problem** is an NP-hard problem in combinatorial optimization.

### Definition

$B = E$ (Ground Set) Set of Graph's Edges

$\mathbf{H} = \{H_1, H_2, \dots, H_m\}$ (Subset System)                                    

$H_j$ Hamiltonian Cycle

Given a graph **G(V,E)** a **Hamiltonian cycle (or Hamiltonian circuit)** is a cycle that visits each vertex exactly once.

$w(H) = \sum_{(u,v)\in H}d_{uv}$ weight of $\mathbf{H}$ Hamiltonian cycle

The **TSP**'s goal is to find the Hamiltonian cycle with the least weight..

![hamiltoniancycle.png](https://github.com/simone-dotolo/TravellingSalesmanProblem/blob/main/media/hamiltoniancycle.png?raw=true)
<p style="text-align: center;">Examples of Hamiltonian cycles</p>

## Local Search (2-opt)

Given an admissible solution for the **TSP**, if we remove two non-adjacent edges, the partial solution can be completed in two distinct ways, only one of which yields a new cycle.

Let $\mathbf{H_0} = \{(u_1,u_2), \dots, (u_{q-1}, u_q), (u_q,u_1)\}$ be a generic Hamiltonian cycle, the **2-opt** move is defined by the following operations:

1. choose a pair of non-adjacent edges $(u_i,u_{i+1})$ e $(u_j,u_{j+1})$
2. remove the edges from the cycle
3. add the two new edges $(u_i,u_{j})$ e $(u_{i+1},u_{j+1})$

The **2-opt** move is performed only if the new cycle has a lower cost than the best cycle found up to this point.

![2opt.png](https://github.com/simone-dotolo/TravellingSalesmanProblem/blob/main/media/2opt.png?raw=true)
<p style="text-align: center;">2-opt move</p>

## MTZ model

The **Traveling Salesman Problem** is a combinatorial optimization problem, and as such, given an appropriate formulation, can be solved using the Gurobi library.

* **Decision variables:**
    - $x_{ij}=1$ if edge $(i,j)\in A$ belongs to the minimum Hamiltonian cycle
    - $x_{ij}=1$ otherwise
 

* **Objective Function:**
    - $min \sum_{(i,j)\in A}d_{ij}x_{ij}$
    
* **Constraints**
    - $\sum_{i:(i,j)\in A}x_{ij}=1$ with $j \in V$ (for each node j exactly one ingoing edge)
    - $\sum_{i:(i,j)\in A}x_{ji}=1$ with $j \in V$ (for each node j exactly one outgoing edge)
    - Subtour Elimination Constraints

**Subtour Elimination Constraint:**

Add decision variables $u_i$ to address the visiting order of vertex $i$ in the solution.
The following constraints are added:
* $0\le u_i \le n-1$
* $u_1 = 0$
* $u_j-u_i \ge 1-n(1-x_{ij})$ with $i,j \in V, i \ne j, j \ne 1$

The following constraints are named MTZ (Muller, Tucker and Zemlin) constraints and are in polynomial number $n^2$.

## Simulated Annealing

**Simulated annealing** is a method for solving unconstrained and bound-constrained optimization problems. The method models the physical process of heating a material and then slowly lowering the temperature to decrease defects, thus minimizing the system energy.

* **Initialization:**
    - consists of finding an initial **S** solution to trigger the algorithm, in this case it was the solution found by the Nearest Neighbor algorithm
* **Defining a move:**
    - defines the operation of randomly locating a new **S'** solution in the neighborhood of the current **S** solution.
* **Move's acceptance:**
    - consists of considering whether or not to accept the **S'** solution as the new current **S** solution.
    
    If $\Delta f$ is less than or equal to 0, the solution **S'** is accepted as the current solution.
    If $\Delta f$ is greater than 0, a random number between 0 and 1 is generated; if this is less than $e^{\frac{-\Delta f}{T}}$, **S'** is accepted, otherwise the current solution remains unchanged.

The initial temperature is $T = \sqrt{n}$, and the decrement law $T_{k+1} = \alpha T_k$ with $\alpha=0.999$.

## Genetic algorithm

Genetic algorithms are based on the analogy between the solution of combinatorial optimization problems and natural selection mechanisms in the genetic field.

* **Problem encoding:**
    - a solution of the problem is represented in terms of a string of decision variables by indicating the possible values of each variable. In this case, a list of nodes indicates the order in which the nodes in the graph will be visited.

* **Initialization and fitness evaluation:**
    - a set of possible solutions forming the initial population is generated and a fitness value is associated with each solution.

* **Selection**:
    - one selects pairs of individuals from the population to which to apply genetic operators (crossover and mutation).

* **Generation of new solutions:**:
    - genetic operators are applied to selected pairs of individuals in order to produce the new offspring.

* **Substitution of population individuals:**
    - one replaces existing individuals in the population with the new offspring produced in the generation step.

How does the crossover operator work in for the **TSP**?
Two crossover points in the two selected individuals are randomly chosen  
![crossover1.png](https://github.com/simone-dotolo/TravellingSalesmanProblem/blob/main/media/crossover1.png?raw=true)  
The segments between the cuts are swapped  
![crossover2.png](https://github.com/simone-dotolo/TravellingSalesmanProblem/blob/main/media/crossover2.png?raw=true)  
This step defines the following correspondences between the nodes:  
<p style="text-align: center;">1 <-> 4 | 8 <-> 5 | 7 <-> 6 | 6 <-> 7</p>  
The offspring will be:
<p>
    <img src="https://github.com/simone-dotolo/TravellingSalesmanProblem/blob/main/media/crossover3.png"/>
</p>  
This procedure is called **Partially Mapped Crossover (PMX).**
