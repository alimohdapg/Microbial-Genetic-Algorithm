# An analytical investigation into how the mutation probability hyperparameter affects the performance of a full microbial genetic algorithm.
## Abstract:
Mutation probability, a biologically inspired operator, is used in genetic algorithms to prevent the convergence to a local minimum. It does this through introducing and maintaining diversity in the genetic algorithm’s genotype population, which is done in practice by flipping genes (binary bits) in a genotype from 0 to 1 and vice versa.<sup>1</sup>

This report aims to answer the research question **“How does the mutation probability used in a genetic algorithm affect performance?”**. To do this, the effects of using 10 different mutation probabilities in a full microbial genetic algorithm are investigated. Particularly, the fitness of the fittest individual within the population at each generation is thoroughly examined whilst looking at factors of growth such as convergence speed, consistency of results, and steadiness of growth. How these change when a smaller generation size is instead used is also a focus of this report.

Quantitative results are generated and discussed, before coming to the conclusion that mutation probabilities of 20% and 30% give the best overall results, with great performance when used with both a small and large generation size.

## Introduction:
This report aims to examine how the mutation probability affects the performance of a genetic algorithm. The main problem being solved by the genetic algorithm is the resource allocation problem, wherein the genetic algorithm aims to maximize the fitness of resources to be allocated.

The report starts by looking at a wide range of different mutation probabilities that allow for there to be a detailed analysis of how each mutation probability compares against the other ones being investigated. The effect of different mutation probabilities on the diversity of the genetic algorithm’s genotype population is also looked into, in addition to how they affect the maximum fitness of genotypes (the fitness of the fittest individual) after a number of generations.

This investigation also aims to evaluate how the performance of a genetic algorithm changes when the same mutation probabilities are used, but with a smaller generation size instead of a larger one. This enables the report to investigate several important factors such as convergence speed (and whether or not it is premature), result consistency, as well as the characteristics of maximum fitness growth with different mutation probabilities. 

The research question being investigated is, therefore, **“How does the mutation probability used in a genetic algorithm affect performance?”**.

## Methods:
Before stating and defining the genetic algorithm to solve the problem, the resource allocation problem is fully explained below:
* It is a problem in which the goal is to find an optimal allocation of resources such that the total cost of resources is under a given constraint (maximum cost), whilst the benefit gained from allocating resources is maximized.
* If B<sub>i</sub> is the benefit of each resource, C<sub>i</sub>  is the cost of each resource, C is the maximum cost, and N is the number of resources available, then:
  * The goal is to maximize: $$\sum_{i}^N B_i$$
  * Whilst remaining under the constraint given by: $$\left(\sum_{i}^N C_i\right) \leq C$$
* All items are distinct.

The specific problem being solved in this report has a maximum cost of 80, the following table lists the 40 items/resources as well as their respective benefit and cost.

<img width="583" alt="image" src="https://user-images.githubusercontent.com/84683922/184544577-6bb36786-13fa-463b-9b0d-ac96b9221b63.png">

## The five parts of the genetic algorithm:
### Initial population:
The initial population consists of 50 individuals, each with a genotype of length 40. All genotypes are initially a 1-dimensional array consisting exclusively of zeros, this allows for an easier way of visualizing how rapidly the fitness of genotypes within the population grows. 
### Fitness Function:
The fitness function is used to determine how fit an individual is by assessing the genotype associated with it and giving it a fitness score. Given a genotype, the fitness function used here calculates the benefit and cost of the genotype by checking the positions of 1s and 0s and looking at how they relate to the benefits and costs arrays that have been previously initialized. Therefore, the length of the benefits array, costs array, and genotype must all be of the same length. 

It then returns the genotype’s benefit as the fitness if the cost is less than the maximum cost. If the cost is greater than the maximum cost, the genotype’s benefit is penalized by being divided by the difference between the maximum cost and the genotype’s cost. This ensures that going over the maximum cost is heavily penalized the further the cost of the genotype is away from the maximum cost. Below is the fitness function’s pseudocode:
```
fitness_function(genotype)
   benefit = []
   cost = []
   for i = 0 to length(genotype)
       benefit.add(genotype[i] * benefits[i])
       cost.add(genotype[i] * costs[i])
   if sum(cost) > max_cost then
       return sum(benefit) / (sum(cost) - max_cost + 1)
   return sum(benefit)
```

### Selection:
From the current population, a random genotype, say genotype_1, is selected. Another genotype is then selected from one of genotype_1’s neighbors, which are defined as the genotypes between the indexes of genotype_1 + 1 and genotype_1 + k, k is a parameter that decides how “local” the neighbors to each genotype are (3 in this case). 

In this genetic algorithm, the 2D array in which the population is stored is considered to be a wrap-around array. The index for the second genotype is therefore obtained by applying the modules/remainder operator, using the size of the population, to the index. This both ensures that indexing out of the array is not an issue whilst still selecting a close neighbor to genotype_1 (considering that the population lies on a circular array). Additionally, this method of selection also combats the genetic uniformity of genotypes across the population<sup>2</sup>, with an established way to evolve genotypes in a way that only, if any at all, promotes local genetic uniformity.

The two selected genotypes are then evaluated by comparing their fitnesses against each other. A winner and a loser are then assigned, wherein the winner is the fitter individual, and the loser is the less fit individual.
### Crossover:
A crossover function is defined as part of this genetic algorithm. This crossover function takes a probability and two genotypes, a winner and a loser, and copies genes from the winner to the loser with the given probability, which in this case is 0.5 (50%). 

The crossover function is one of, if not the most, significant parts of the genetic algorithm. This is because it allows the algorithm to preserve the fittest individual, whilst still giving it the ability to create a new “offspring” that may have a better fitness than the fittest individual. This is also in part one of the benefits of the mutation probability which will be further explained below. The following pseudocode showcases the crossover function, rand_num is a random number drawn from a uniform distribution of numbers between 0 and 1.

```
crossover(w, l, probability)
   for i = 0 to length(l)
       if rand_num > probability then
           l[i] = w[i]
```

### Mutation:
The defined mutation function flips the genes (bits which are either 0 or 1) of a given genotype, with a given probability. This allows for the formation and introduction of new genes, therefore maintaining diversity in the population. Moreover, it also prevents the premature convergence of the genetic algorithm as new variations in genes are introduced.

As the mutation probability is the hyperparameter being deeply investigated in this report, 10 different mutation probabilities were used. These were 0.1, 0.2, …, 0.9, 1.0. The below pseudocode showcases the mutation function and what it does. As was the case with the crossover function, rand_num is a random number drawn from a uniform distribution of numbers between 0 and 1.
```
mutate(genotype, probability)
   for i = 0 to length(genotype)
       if rand_num < probability then
           genotype[i] = genotype[i] XOR 1
```

## The genetic algorithm:
Having listed and explained the five main parts of the genetic algorithm, the genetic algorithm aiming to solve the problem is given below in its most compact form. For more detailed pseudocode of the genetic algorithm, refer to the appropriate section in the appendix.
```
full_microbial_ga(mutation_prob, crossover_prob, generations, k)
   Generate Initial Population
   for i = 0 to generations
      Selection
      Compute Fitnesses
      Assign Winner and Loser
      Crossover
      Mutation
      Replace Genotype at Loser Index with New Genotype
```

The number of generations, which is known beforehand, is the parameter that controls how many times the algorithm runs before stopping. As the emergence point is unknown, a higher number of generations, when all other parameters are fixed, usually results in a higher maximum fitness as more opportunities for the mutation rate causing new beneficial variations in genotypes emerge.<sup>3</sup>

An alternative to this approach is looking at when the algorithm converges, meaning that no great change over several iterations has occurred, and using that to decide when the algorithm terminates. However, as this may favor some mutation probabilities over others, a fixed number of iterations/generations of the genetic algorithm was chosen instead.

## Results:
The following graphs show the maximum fitness of all genotypes over 100,000 generations; 10 trials were performed for each mutation probability being examined. The black line shows the mean of the maximum fitness over all trials, while the gray area above and below the line shows the standard error over all trials.

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84683922/184544850-c46afd9a-9e47-4e15-8824-b3eb6337f146.png">

<img width="700" alt="image" src="https://user-images.githubusercontent.com/84683922/184544871-2f11b31d-e9eb-4312-adeb-e9b8e5f1ebc7.png">

While these graphs may all seem similar, it's important to take a look at the axes, specifically the y-axis, graphs with mutation probabilities that resulted in a higher maximum fitness have a higher y-axis value at the top, which is 160. This indicates that the maximum fitness is close to, or over the value of 160, unlike the other graphs with 140 as the highest y-axis value.

Looking at the graphs, it can be inferred that using a mutation probability of 10% gives the best results, especially with a larger number of generations as used here. The graphs show steady growth that is consistent throughout different trials as shown by the minimal variance in the standard error.

The graph for the mutation probability of 20% showed the second to best results after the graph using a mutation probability of 10%. It also showed similar steady growth to that graph. However, it can be noticed that a few bumps in the line are present; these indicate the discovery of a higher max fitness in the population. With that being said, it still falls short when being compared to the graph using a mutation probability of 10% as the 160 on the y-axis is at a higher place (indicating a lower max fitness at the end).

The graphs for mutation probabilities 30%, 40%, 50%, and 60% all show a similar way of growth, with uneven increments at the start, followed by large increments between the midway point and the point of termination in the graph. It can also be noted that the maximum fitness in each graph gets lower as the mutation probability is increased; this conclusion can be arrived at by looking at the y-axis, which pushes the 140 closer to the top as the mutation probability increases. The graph for the mutation probability of 50% shows the highest variance in standard error throughout the graph; this indicates that choosing this mutation rate may be an unreliable choice and is therefore unfavorable.

For the mutation probabilities 70%, 80%, and 90%, we see that the max fitness, as shown by the y-axis, is similar and almost identical across all 3 graphs. The max fitness also rarely changes value during the generations of 80,000 and 100,000. The only exception to this is the large increment in the graph of the mutation probability of 80%, at generation number ~80,000. However, this may be a rare occurrence as indicated by the standard error following the increment.

The final graph showing the max fitness over generations when using a mutation probability of 1.0 shows many unique characteristics. Using a mutation rate of 100% essentially means that all genes/bits of any given genotype are flipped. While one would expect this to be meaningless, it showed surprising results, ending up with a maximum fitness which is ≈ the maximum fitness of the graph using a mutation probability of 20%. Unlike that graph, however, it didn’t show steady growth but rather increased the max fitness in large increments as indicated by the large jumps across the line.

## Analysis & Discussion:
The fact that using a mutation probability of 10% gives the best results does not come as a coincidence. As the training loop consisted of 100,000 generations, it was possible for a variety of different genotypes to be tested (positively contributing to the population’s diversity). Additionally, it also enabled the 10% probability to be a ~10% probability in practice as well, since the probability is averaged over a large number of runs.

The second best mutation probability, 20%, also shares similar characteristics to using a mutation probability of 10%. It also has its mutation probability averaged to ~20% over several runs due to the large generation size. This allows it to generate a variety of different genotypes that contribute to the genetic algorithm’s ability to maximize the fitnesses of genotypes within the population.

As shown by the results of the graphs, a high mutation probability (that isn’t 100%) does not give the best max fitness for genotypes. This can be attributed to the fact that optimal solutions that could have been generated if a low mutation probability is used, are lost with a high mutation probability. 

If the genetic algorithm’s performance has a high priority when solving an optimization problem or doing an experiment, then one of the hyperparameters which needs more consideration is the generation size. The results presented in the above graphs only consider the large generation size of 100,000. As they may not show the performance of mutation probabilities at smaller generation sizes, I decided to look into the performance of different mutation probabilities at a much smaller generation size, 100 generations. Looking into a smaller generation size also gave me the ability to look into what exactly happens within the earlier generations of the training loop, which may be a crucial part of different experiments and projects.

<img width="451" alt="image" src="https://user-images.githubusercontent.com/84683922/184544965-fcf6ddd0-6812-4ae4-b972-bdecb23e7f7d.png">

<img width="472" alt="image" src="https://user-images.githubusercontent.com/84683922/184544984-eb19150c-94fd-4494-a0cd-b802af012d18.png">

Surprisingly, these results show that when a smaller generation size is used, mutation probabilities between 20% and 60% give the best results. These are then followed by larger mutation probabilities which score satisfactory results as well. However, the mutation probability of 10%, which gave the best results when used with a larger generation size, now gives the worst results. 

Although it might seem surprising, the reason for this becomes clear when looking at the line graph’s results. We can see that the max fitness steadily improves with each generation, even when coming close to generation #100, while other mutation probabilities start slowing down when it comes to max fitness improvements. 

Furthermore, the use of a 10% mutation probability may be favorable if consistent results are needed, as this mutation probability minimizes the standard error throughout all generations in the training loop. Other mutation probabilities such as 50% for example, although were able to give high results for the final max fitness, exhibited a much higher standard error as indicated by the line graph.

Combining the results of experiments using a large generation size with those of one with a small generation size, the mutation probabilities of 20% and 30% seem like the ideal mutation probabilities overall. Coming close to the max fitness given by the 10% mutation probability when a large generation size is used, while still maintaining their performance with a small generation size.

A possible future improvement for this genetic algorithm is a better fitness function that penalizes genotypes that go over the maximum cost less strictly. The current implementation divides the fitness by the genotype’s cost - the maximum cost. While this ensures that genotypes that don’t exceed the maximum cost are favored, it discourages genotypes from even exceeding the maximum cost by a few points. A better implementation would be to instead penalize based on the percentage of cost exceeding the maximum cost, which would result in less severe but still significant penalties.

The population size, which was set to 50 for this experiment, is another parameter that may require more fine-tuning if the genetic algorithm’s performance has a high priority. Although it may make it possible to maintain diversity within the population, setting it to a value that is too high leads to a direct increase in computational cost, which needs to be minimized for that case. 

One important shortcoming of this study is that it didn’t consider mutation probabilities in increments of 1, and instead considered them in increments of 10. While this made it so that results could be as general as possible, the results weren’t very specific. If a mutation probability such as 15% for example gave the best balance of results, this study wouldn’t have been able to conclude that. The smaller sample size of 10 mutation probabilities, however, enabled there to be a detailed overview of the performance of each mutation probability.

The results this study gave may also be tied to the other hyperparameters (crossover probability, population size, genotype size, generation size, local neighbors size) chosen at the very start. The modification of any of these hyperparameters may lead to vastly different results not covered in this study.

## Conclusion:
In conclusion, this study gave a detailed view of how the mutation probability hyperparameter affects the fitness of genotypes over generations in a full microbial genetic algorithm. It showed that when a large generation size is used, smaller mutation probabilities (<40%) give the best and most consistent results. The mutation probability of 10% especially thrived here, giving the best and only final max fitness that is above 160. 

The study also analyzed how these results change when a smaller generation size is used instead. This part gave surprising results where mutation probabilities between 20% and 50% gave the best results. The mutation probability of 10%, however, showed the worst results here unlike the previous experiment, with a disappointing final max fitness of 97.0. 

Combining these two experiments then allowed the study to conclude that the mutation probabilities of 20% and 30% give the best overall results. Giving great performance when used with both a small and large generation size. 

This study also concluded that larger mutation probabilities (>50%) gave decent, unexceptional results in both cases. They also displayed the highest amounts of standard error from the mean throughout both experiments’ training loops. The only exception is the mutation probability of 100%, which gave surprisingly extraordinary results when used with a higher generation size. Using it, however, led to inconsistent, un-steady growth with reliance on large increments to increase the maximum fitness.

Future areas of study that aim to build upon the results of this study include trying out a less strict fitness function, looking at a wider range of mutation probabilities, as well as conducting a thorough investigation of how the other hyperparameters of the genetic algorithm affect performance.

## Bibliography:
1 tutorialspoint.com. “Genetic Algorithms Mutation.” Www.tutorialspoint.com, 2019, www.tutorialspoint.com/genetic_algorithms/genetic_algorithms_mutation.htm.

2 Harvey, Inman. The Microbial Genetic Algorithm. 13 Sept. 2009.

3 Vrajitoru, Dana. Large Population or Many Generations for Genetic Algorithms? Implications in Information Retrieval. 2000.

4 Keet, Maria. “Genetic Algorithms - an Overview.” Www.meteck.org, 2002, www.meteck.org/gaover.html. Accessed 11 Apr. 2022.

5 Roeva, Olympia, et al. Influence of the Population Size on the Genetic Algorithm Performance in Case of Cultivation Process Modelling. 2013.
## Appendix:
### Full Code:
See Report_2.ipynb file.

### Full Genetic Algorithm Pseudocode with Max Fitness Tracking:
```
full_microbial_ga(mutation_prob, crossover_prob, generations, k)
   num_genes = 40
   num_individuals = 50
   genotypes = Matrix of zeros, shape (num_individuals, num_genes)
   fitnesses = Array of zeros, shape (Population Size)
   max_fitnesses = []
   curr_max_fitness = 0
   for i = 0 to generations
       g1_index = A random int between 0 and num_individuals
       g1 = genotypes[g1_index]
       g2_index = (A random int between g1_index + 1 and g1_index + 
                   k) % Population Size       
       g2 = genotypes[g2_index]
       if fitnesses[g1_index] >= fitnesses[g2_index] then
           w = g1
           l = g2
           l_index = g2_index
       else:
           w = g2
           l = g1
           l_index = g1_index
       crossover(w, l, crossover_prob)
       mutate(l, mutation_prob)
       genotypes[l_index] = l
       l_fitness = fitness_function(l)
       fitnesses[l_index] = l_fitness
       if l_fitness > curr_max_fitness then
           curr_max_fitness = l_fitness
       max_fitnesses.add(curr_max_fitness)
   return max_fitnesses
```
