# Slide 1

Good morning everyone, today I will present my thesis on "Exploring the Use of LLMs for Agent Planning".

# Slide 2

In the past few years, the diffusion and the development of Large Language Models has revolutionized the field of AI.

# Slide 3 -> 6

These models are trained on vast amounts of textual data and are able to generate human-like text with correct grammar and syntax.
Not only. Thanks to the variety of the data used in the training process, they are able to perform tasks they were not explicitly trained for, the so called "emerging abilities", such as mathematical reasoning, planning abilities and more.

# Slide 7 -> 10

And in the planning field falls this work. In the literature, most of the LLM-based planning approaches rely on additional a-priori frameworks on top, such as Chain-of-Thought reasoning, few-shot prompting or fine-tuning the models.

# Slide 8

But we asked "What happens if we strip everything away? Can LLMs plan and navigate effectively in an unknown environment? How well can LLMs generate sequential decisions in such environments?".

# Slide 9

To explore this, we placed an agent, driven by an LLM inside an environment to solve a logistic task, that is to pick up and deliver parcels.

# Slide 10

Our final implementation was like this.

# Slide 11

For the environment, we used Deliveroo.js, a web-based game where the agent is placed inside a bidimensional map with parcels that spawn all around the map with the goal of picking them up and deliver them to a delivery tile.

# Slide 12

To evaluate the performance, we used the KnowNo framework, introduced by Google, that allows us to compute the model's uncertainty at each step of the agent. In brief, it works by first asking the model to choose an action between a set of possible actions, where each action is mapped to a specific token. Then, we introduce a bias towards those tokens in order to "force" the model to choose one of them. We retrieve the log-probability of each token and we compute the certainty of the model by applying the softmax. Then, only the values above a certain threshold are kept.

# Slide 13

To query the model, we followed a prompting strategy based on the literature. We first give the model a role, then we add the map description without parsing the information received from the server to simulate the "unknown" and "unpredictable" environment; we just remove the JSON syntax, so the parentheses and so on. Then, at the end, we ask to choose the best next-action between up, down, left, right, pickup and putdown.

# Slide 14

Talking about the model used to drive the agent, we sticked to OpenAI models due to some limitations in the implementation of the uncertainty framework. We saw that the newer the model, the better the performance. GPT-4o was the last model released and so the most powerful one, but the performance of the mini version were in line with the full version at a fraction of the cost, so we based our experiments on that.

# Slide 15

Now that the pieces are in place, let's talk about how we collected the data.

# Slide 16 -> 18

First, we scanned the entire map cell by cell with the agent and we saved the uncertainty values of the retained actions, or actually, the certainty, creating what we call the "heatmaps".

# Slide 19

Then with this information, we computed for each cell the list of actions that were correct, and we kept, from the heatmaps, only the probability of the correct actions, creating what we called "Correctness heatmaps", where, in each cell, we can see the probability of choosing the correct action.

# Slide 20

Our tests relied on two goals: the pickup and the deliver. The difference between them is that in the pickup, the location of the goal is explicit so it states something like "take the parcel at the cell X, Y", while in the deliver goal, the model has to infer the coordinates of the delivery tile from the map description that in our case was the list of all the available cells.

# Slide 21

For the agent implementation itself, it was an iterative process, and in the end we had two versions: the "Stateless agent", where each query was a new interaction with the LLM, and the "Stateful agent" where the model was provided with the entire conversation history and so it could leverage its "few-shot learning" capabilities by having the history of actions-result pairs.

# Slide 22

Now let's see the results.

# Slide 23 -> 25

First of all, earlier I said that we wanted to test an unknown environment. The way we added the map to the prompt didn't include any information about the orientation of the world. Our main tests were analyzed treating the map as a "matrix by developers", with the (0,0) cell in the top-left corner. We also wanted to test the "cartesian origin" in the bottom-left corner, and in this example, where the darker the green of the cells the higher the correctness of the actions, we can see that the model has a bias towards perceiving the origin in the top-left corner. This may be due to the way the model is trained, with a lot of code in the training set and also it seems that they used 2 epochs for text-based data and 4 epochs for code-based data. Another possible explanation is that the way the model is presented with the data resembles the JSON syntax, activating the "code" part of the network.

# Slide 26

Here we can see that there is 30% less cells with the correct action being the one with the highest probability.

# Slide 27

We also noted some common uncertainty patterns.

# Slide 28 -> 29

These correctness heatmaps are here as an example that highlights

# Slide 30 -> 35

This is not limited to maps where the goal was central. If we isolate the top-left quadrant of this 13x13 map, we see a similar behavior to the 7x7 map with the goal in the bottom right corner. On the same way, if we isolate the bottom-left quadrant and see the 7x7 map with the goal in the top right cell we again see a similar behavior.

# Slide 36

the model's behavior near the goal. We can see that the top-right quadrant is the most uncertain, and this is consistent across all the maps we tested.

# Slide 37 - > 39

This analysis is also useful to understand the behavior of the stateful agent. Let's see this example, of a 13x13 map with the pickup goal in the center. We can see that the agent follow an optimal path, but it goes back and forth one time. If we compare this to the stateless, we see struggles in the same cells, with the difference that the stateful agent is able to ultimately reach the goal, while the stateless has the 0% probability of choosing to go right in those two cells. This is the power of the context and history. Last, the stateful implementation resulted in around a 20% less required actions to reach the goal compared to the stateless agent, with the drawback of the context size in bigger maps that limits the agent's ability to successfully navigate.

# Slide 40 ?

We also say that, in the same portion of the maps, the behavior is similar but not identical. In this example we see the eight cells around the goal that have similar probabilities but a decaying trend in the correctness as the size of the map increases.

# Slide 41

To summarize, we saw that the generative capabilities of the LLMs in planning are effective, but with consistent problems and biases. With the stateful agent we saw an average of 20% less action required to reach a goal, with the limitation of the context size in big maps. Another important factor is that the percentage of "problematic" cells is quite similar as the size of the map increases, but translating this to real numbers result in an increasing difficulty of an agent to escape a problematic zone if it goes deeper inside it due to a series of unlucky actions.

# Slide 41

Thank you

# OLD

As we all know, in the recent years, AI has made remarkable progress.

One of the most impactful advancements is the development of Large Language Models, that made a huge impact on the entire field of AI.

LLMs are trained on an enormous amount of textual data and are able to generate human-like text with a correct grammar and syntax. Not only.

Thanks to the variety of the data used in the training process, these systems manifest emerging behaviors such as mathematical reasoning and planning even though they were not explicitly trained for these tasks.

In this thesis, we ask: Can LLMs plan effectively without extra fine-tuning or specialized frameworks on top?".

Most LLM-based planning approaches available in the literature rely on additional frameworks, such as fine-tuning, Chain-of-Thought prompting, or even hybrid reinforcement learning models.

These techniques aim to enhance planning by providing structured reasoning pathways.
But what happens if we strip everything away? TODO: change to "strip anything prior"

With this work, we wanted to analyze the generative capabilities in planning intrinsic in LLMs.

Our main research question is simple:
"Can an LLM, without additional training or frameworks, effectively plan and navigate in an unknown environment?"
To explore this, we placed an LLM-driven agent inside a web-based game where it must pick up and deliver parcels. This approach allows us to evaluate how well LLMs can make sequential decisions in real-time environments.

I can't stress this enough, this is a generative approach and we want to see the intrinsic capabilities of an LLM in planning.

For our experiment we used the web-based platform Deliveroo.js developed by prof. Robol for the course Autonomous Software Agents. This platform allows us to drive an agent in a 2D environment with parcels that spawn all around the map with the goal of picking them up and delivering them to a destination.

A little before I said "evaluate how well LLMs can make sequential decisions". The keyword here is "sequential". Our approach relies on asking the model to give us only the next action to take given the current state of the environment, it's a "step-by-step" approach. This allows us to evaluate the model's uncertainty at any moment using a framework called KnowNo, introduced by Google.

This framework works like this: we ask the model for an action mapped to a specific token. We introduce a bias towards the tokens that represent all the possible actions in order to "force" the choice of the model to be one of the possible actions. We then retrieve the log-probability of each token and we compute the certainty of the model by softmaxing the log-probabilities. Then, all the values below a certain threshold are discarded.

A quick recap, the idea is like this: we have the agent in the game, we ask the model what to do next given the current state, we retrieve the uncertainty using KnowNo and we use this information to analyze the model's capabilities.

Now, let's rewind a bit and talk about the structure of our agent. To ensure fair testing, we structured our prompts based on established literature.
We provided the LLM with:

1. A description of the current state of the environment.
2. A limited set of possible moves (up, down, left, right, t, s).
3. A request to select the best move, outputting only a single token.

At the start I said "unknown environment", because one of the idea was to give a sense to this research, by maybe applying this approach to a situation where the environment changes in between the steps, making impossible to use a method that relies on a long planning before acting.

To simulate this, the map is put in the prompt in its raw from as the server returns it (only without the JSON syntax). This way the model has to understand the map structure by itself and adds a level of both complexity and robustness to the experiment, since the model has to understand the map structure by itself but also, if this approach is applied to a different style of map, the agent is still able to navigate.

About the model that drives the agent, we need to briefly talk again about the uncertainty computation framework. As I said, it uses a bias towards the possible actions to force the model to choose one of them.
Using OpenAI API, this bias is implemented by a parameter in the API call; while there is no framework that let us run Open Source models that implements this behavior. To implement ourselves this logit bias we would have needed to load the model, modify its architecture to try to have the same behavior. Since, thanks to the last model released, the price of the API has been reduced by a lot, we decided to use the a GPT model.

The data collected has been then used to build specialized heatmaps that show the model's uncertainty in every cell of the map. And the output is like this. We can see [explain the heatmap]

Not only, after that, we computed, for each cell, the list of correct actions and we created another heatmap that shows the model's probability of choosing the correct action in every cell of the map. The output is like this. We can see [explain the heatmap]

We call this approach "Stateful agent", since, if we let the agent run, at each step a new instance of the "conversation" with the model is created.
On the other hand, the "Stateless agent" provides the model the entire conversation history, allowing the model to leverage its "few-shot learning" capabilities.

Still, the heatmaps created by the stateful are a great indicator also of the stateless agent's behaivor.

Indeed we can see that if we let an statelss agent run in a 7x7 map with the goal in the bottom right tile, we can see that near the goal the agent struggles to find the correct path, while in the other parts of the map the agent is able to navigate quite well. And this can be seen in the heatmap generated for the same game map.

# Results

Now that the project structure is clear, let's take a look at the results.

First, I want to remind you that we did not provide any information about the position of the origin on the map. However, we can observe a bias towards the origin tile, which is positioned in the top-left corner.

Additionally, we notice some uncertainty trends that persist even as the map scales. For example, in this case, the top-right quadrant exhibits more uncertainty, as does the bottom-left quadrant. Meanwhile, the top-left and bottom-right quadrants are almost perfect in terms of accuracy.

Another key observation is that the row containing the goal, as well as the upper part of the column containing the goal, show significant uncertainty. In some cases, the correct action is even removed completely by the uncertainty framework. Furthermore, as the map size increases, we see a slight reduction in top scores.

This also reveals another important trend: the top-right quadrant consistently exhibits uncertainty. If approximately 10% of the cells in that area are uncertain, marking them accordingly makes it easier for an agent to escape the problematic zone. However, with an unlucky sequence of actions, the agent might remain stuck in a larger map.

Next, we tested both the pickup and deliver prompts. The pickup goal differs from the deliver goal in a crucial way: in the pickup task, the goal’s position is explicit, whereas in the deliver task, it is implicit. The model has to retrieve information about the goal’s location, which may be in a different zone of the map—sometimes quite far away. Our tests confirmed this distinction.

There is an article "Needle in a Haystack" that demonstrates how large language models tend to assign more importance to the top and bottom parts of a prompt. We also observed that the behavior related to different quadrants of the map remains consistent, even when the goal is positioned differently. For example, in cases where the goal is placed in the bottom-right corner, the behavior remains nearly perfect—except for uncertainty along the row and column where the goal is located. This aligns with the trends we observed in the original map.

We also compared different portions of maps. Specifically, we examined the tiles surrounding the goal across various map sizes. While the model’s behavior remained similar, it was not identical. This highlights the fact that the way the map is processed by the LLM affects its behavior, particularly near the goal.

To further test our findings and determine whether the biases we observed were due to our methodology or inherent limitations of the model, we conducted additional experiments. First, we tested the model without the pickup and deliver actions. Instead, we simplified the problem by making the goal explicit. The resulting behavior remained almost identical.

Another key experiment involved breaking down the navigation task into two subproblems:

Identifying the neighboring cell that is closest to the goal.
Determining the correct action to reach that selected cell.
However, we encountered an issue: in some cases, multiple cells provided the same level of proximity to the goal. If the model selected the wrong cell, the subsequent action—no matter how accurate—would not contribute to reaching the goal.

To summarize our findings:

Stateless approaches exhibit consistent behavior across different maps and goal placements. However, near the goal, there is always some level of uncertainty. Certain zones in the map remain highly uncertain.
Stateful approaches allow the agent to track past actions, which helps it escape problematic zones. However, in larger uncertain areas, the agent is more likely to become stuck.
Lastly, we compared the models used in our experiments. Specifically, we utilized GPT-4 Mini for these tests.
