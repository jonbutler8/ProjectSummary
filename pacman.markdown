---
title: AI 2017: Pacman
---
[Back to Project Summary](https://jonbutler8.github.io/ProjectSummary/)
#### [Artificial intelligence: Berkeley Pacman (Python)](/pacman.html)
The University of California Berkeley's computer science department provides a set of public project specifications for teaching the fundamental concepts of artificial intelligence. The projects, which consist of various challenges surrounding a python Pacman implementation, can be found [here](http://inst.eecs.berkeley.edu/~cs188/pacman/project_overview.html).

At RMIT in 2017, our introductory AI course used these projects as part of assessment. Each student was required to complete their own individual implementations of three projects: [tree search](http://inst.eecs.berkeley.edu/~cs188/pacman/search.html), [multi-agent (adversarial) search](http://inst.eecs.berkeley.edu/~cs188/pacman/multiagent.html) and [reinforcement learning](http://inst.eecs.berkeley.edu/~cs188/pacman/reinforcement.html). 

For the fourth assignment, students formed groups and competed against one another by creating Pacman agents to participate in a [capture the flag contest](http://ai.berkeley.edu/contest.html). In this final assignment, our team placed 3rd out of 30 student teams and was chosen for participation in an upcoming intra-university tournament between RMIT and University of Melbourne.




# "Turing Complete" team Pacman Agent
Note: UC Berkeley requests that code solutions to these assignments not be published, and so I have included below only a summary of the strategy our Pacman agent employed.
## Approach
Our agent uses a goal-based heuristic search approach to control its behaviour.

This means that our agent calculates a set of goals based on the current state of the board (e.g., location of food), and then sets about trying to reach them. Using a heuristic A\* approach, our agent looks finds the most favourable path to one of its goals. Depending on the heuristic applied, this may not actually be the shortest path - a shorter path may be avoided if it would take the agent through unfavourable terrain or put it at risk.

The agent will follow its chosen path on subsequent turns unless circumstances change to make the path unfavorable. For instance, a path may become unfavorable if enemy agents are nearby or the goal grid changes (e.g. our team-mate is killed and deposits his food back to the board). When this occurs, the path is recalculated. This is done to adapt the adversarial nature of the problem, with enemy moves being uncertain. We implemented this by giving the agent a "carefreeness" property calculated with a simple function that indicates how the agent feels about the current uncertainty. 

## Analysis
The technique that we employed (heuristic search), allows us to be pretty flexible.

Though in the worst case A\* performs very poorly, we can use a heuristic to avoid the worst case. The time complexity is less than that of a look ahead agent (like min-max or expectimax), and thus allows us the agent to avoid time penalties. If time constraints do become a problem (tested by a time comparison at each search node expansion), the agent is able to terminate its search turn early by executing the first move in its explored path. On the other hand, a planning or learning agent that calculates an optimal policy before-hand would use minimal calculation during a move and thus have a turn time complexity superior to our agent. However, these agents would need time before the game to precompute and have to work within the 15 second limit, whereas our agent utilizes only a small amount of pre-computation.

In terms of optimal move choice, our agent is sub-optimal - the hidden state information makes it difficult to create an agent that will make the optimal move in all cases. We considered using particle filtering to narrow in on opponents (with reference to Berkeley assignment 4), but decided against it as we felt we didn't have time to develop a working algorithm, and that completing that assignment would be a large task onto itself. An approach that pre-calculates a good policy would also be good, but unnecessary - we already have a good idea what a good policy for Pac-Man looks like. The search approach allows us to directly tell an agent what it should be doing (by setting goals to search for), while leaving the agent calculate best course of action to complete its task. Furthermore, using multiple search heuristics for different situations allow us to steer the agent and influence how it performs actions (for instance, telling it to search for the goal in places that aren't near an enemy first) without telling it exactly what action to perform.

## Development
### Early Agents
Before settling on our final approach we trialled a number of others. We decided that in an adversarial game, a look-ahead approach that guessed enemy moves and tried to pick an approach based on them would work well. One problem we foresaw was that the look-ahead would take too long to calculate. We knew the branching factor would be too high for min-max, so we tried an expectimax approach. The reason for this is we could heuristically guess an opponent move and not have to branch on min nodes. Even with this approach the time cost of the branching factor became a hindrance. If the look-ahead analyzed more than three turns in advance, it would run too slowly. As such, the agent behaved similarly to a reflex agent with a very limited look-ahead that did little to improve the agent's behaviour. When we submitted it to the preliminary tournament, it failed almost every game. 

Another approach that we implemented was reinforcement learning. A true q-learning approach would explore prohibitively many states, and would need to have test runs for every possible board. However, an approximate q-learning approach would easily allow us to take lesson from one situation and apply them to another and would need much less space. We designed an approximate q-learning agent with features that we believed were relevant to the problem. Weights could be saved in an output file and used in future runs. Using this as a base, we created a training script to continually train the q-learning agent against itself and other agents. In practice, we found that while we could train a reasonable learning agent, it wasn't very smart. It was often difficult to coerce the agent to find rewarding states, and so we also used a combination with the Berkeley provided keyboard agents to manually control approximate q-learning agent to jump-start its knowledge of desirable features. However, the agent would often get stuck in training ditches and learn incorrect behaviour. A q-learning approach could possibly work with this task, but we felt the agent would need fairly complicated features. After investing too much time without much benefit, we decided that a simpler approach was better.

### Heuristic Search Agents
We decided on a heuristic search approach because it allowed us to easily tell the agent what tasks we want to it achieve, but still require the it to calculate the best approach to that task. Our first attempt at this was to create a team with two different agents, using a superclass with core methods subclassed by a defensive and offensive subclasses. The offensive agent would seek food from the opponent’s side of the board until it was carrying a certain amount, then it would change its goal to find the nearest drop-off point to secure its score. The defensive agent would look for an opponent, and if none were found, would sit near the border between board halves until an enemy drew near.

This team performed relatively well in preliminary tournaments. However, after watching the replays we realised that the defensive agent was often idle - particularly on large boards. When trialling our offense-defence agents against a team of two identical offense agents, our original team was outperformed.

As such, we decided to create one flexible agent that would dynamically change its overarching goals, and then use A\* to locate them. Both agents in the team would start out aggressive until a certain amount of food was gathered, capture it, then decide upon capturing to either defend borders or get more food, based on enemy actions and point totals. Agents could estimate invading opponents by taking into account food that was "missing" from their side of the board (no longer present, but not contributing to a score difference).

## Final Thoughts
Given the nature of the problem we ultimately decided upon a heuristic search based agent, as this agent could dynamically calculate the best course of action, while allowing us freedom to specify broadly what that course of action might look like. After multiple adjustments and improvements, we arrived at an agent which performs admirably in its task.

[Back to Project Summary](https://jonbutler8.github.io/ProjectSummary/)

