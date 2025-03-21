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

To explore this, we placed an agent, driven by an LLM inside an environment to solve a logistic task, that is to pick up and deliver parcels. This was designed to to analyze the generative capabilities in planning intrinsic in LLMs.

# Slide 10

Our final implementation was like this.

# Slide 11

For the environment, we used Deliveroo.js, a web-based game where the agent is placed inside a bidimensional map with parcels that spawn all around the map with the goal of picking them up and deliver them to a delivery tile.

# Slide 12

To evaluate the performance, we used the KnowNo framework, introduced by Google, that allows us to compute the model's uncertainty at each step of the agent. In brief, it works by first asking the model to choose an action between a set of possible actions, where each action is mapped to a specific token. Then, we introduce a bias towards those tokens in order to "force" the model to choose one of them. We retrieve the log-probability of each token and we compute the certainty of the model by applying the softmax. Then, only the values above a certain threshold are kept.

# Slide 13

To query the model, we followed a prompting strategy based on the literature. We first give the model a role, then we add the map description without parsing the information received from the server to simulate the "unknown" and "unpredictable" environment; we just remove the JSON syntax, so the parentheses and so on. Then, at the end, we ask to choose the best next-action between up, down, left, right, pickup and putdown.
Moreover we tested also the style of the prompt in "simplified" problems, getting comparable results indicating that this direction was correct.

# Slide 14

Talking about the model used to drive the agent, we sticked to OpenAI models due to some limitations in the implementation of the uncertainty framework. We saw that the newer the model, the better the performance. GPT-4o was the last model released and so the most powerful one, but the performance of the mini version were in line with the full version at a fraction of the cost, so we based our experiments on that.

# Slide 15

Now that the pieces are in place, let's talk about how we collected the data.

# Slide 16 -> 18

First, we scanned the entire map cell by cell with the agent and we saved the uncertainty values of the retained actions, or actually, the certainty, creating what we call the "heatmaps".

# Slide 19

Then with this information, we computed for each cell the list of actions that were correct, and we kept, from the heatmaps, only the probability of the correct actions, creating what we called "Correctness heatmaps", where, in each cell, we can see the probability of choosing the correct action.
These two kind of heatmaps allowed us to analyze step-by-step the behavior of the agent.

# Slide 20

Our tests relied on two goals: the pickup and the deliver. The difference between them is that in the pickup, the location of the goal is explicit so it states something like "take the parcel at the cell X, Y", while in the deliver goal, the model has to infer the coordinates of the delivery tile from the map description that in our case was the list of all the available cells.

An important aspect to underline here is that in the delivery goal, the results were in line with the pickup goal, with a slightly higher uncertainty overall and a difference in the top1% score of 6% less on average, still showing that the model is able to infer the goal position from the map description.

# Slide 21

For the agent implementation itself, it was an iterative process, and in the end we had two versions: the "Stateless agent", where each query was a new interaction with the LLM, and the "Stateful agent" where the model was provided with the entire conversation history and so it could leverage its "few-shot learning" capabilities by having the history of actions-result pairs.

# Slide 22

Now let's see the results.

# Slide 23 -> 25

First of all, earlier I said that we wanted to test an unknown environment. The way we added the map to the prompt didn't include any information about the orientation of the world. Our main tests were analyzed treating the map as a "matrix by developers", with the (0,0) cell in the top-left corner. We also wanted to test the "cartesian origin" in the bottom-left corner, and in this example, where the darker the green of the cells the higher the correctness of the actions, we can see that the model has a bias towards perceiving the origin in the top-left corner.

# Slide 26

Here we can see that there is 30% less cells with the correct action being the one with the highest probability.`
This may be due to the way the model is trained, with a lot of code in the training set and also it seems that they used 2 epochs for text-based data and 4 epochs for code-based data. Another possible explanation is that the way the model is presented with the data resembles the JSON syntax, activating the "code" part of the network.

# Slide 27

We also noted some common uncertainty patterns.

# Slide 28 -> 29

For the first one, I put here a subset of maps just to show a consistency in an overall trend of less correctness in the top-right quadrant of the map and also in the bottom-left one, even if in reduced form.
And on average over all of our tests, we saw a difference of 35% between the top-left and the top-right quadrant and again, this behavior is consistent across all the maps we tested, with a slight reduction in the general correctness as the size of the map increases.

# Slide 30 -> 35

This is not limited to maps where the goal was central. If we isolate the top-left quadrant of this 13x13 map, we see a similar behavior to the 7x7 map with the goal in the bottom right corner. On the same way, if we isolate the bottom-left quadrant and see the 7x7 map with the goal in the top right cell we again see a similar behavior.

# Slide 36

Another common patter is the model's behavior near the goal. We can see a trend where the entire row and the upper part of the column containing the goal exhibit high levels of uncertainty. In some cases, the correct action is even removed completely by the uncertainty framework. This may be linked to how the LLM perceives the map or due to the fact that, in those cells, the model has only one correct action that has to select, while in all the other cells two actions are correct.

# Slide 37 - > 39

This analysis conducted using the stateless agent is also useful to understand the behavior of the stateful agent. Let's see this example, of a 13x13 map with the pickup goal in the center. We can see that the agent follow an optimal path, but it goes back and forth one time. If we compare this to the stateless, we see struggles in the same cells, with the difference that the stateful agent is able to ultimately reach the goal, while the stateless has the 0% probability of choosing to go right in those two cells. This is the power of the context and history. Last, the stateful implementation resulted in around a 20% less required actions to reach the goal compared to the stateless agent, with the drawback of the context size in bigger maps that limits the agent's ability to successfully navigate.

# Slide 40 ?

We also say that, in the same portion of the maps, the behavior is similar but not identical. In this example we see the eight cells around the goal that have similar probabilities but a decaying trend in the correctness as the size of the map increases.

# Slide 41

To summarize, we saw that the generative capabilities of the LLMs in planning are effective, even though challenges such as consistent problems and biases remains. While LLMs aren't yet fully capable of reliably plan even just the next action, we believe that the limitations highlighted in this thesis may offer valuable insights for future advancements and refinements.

# Slide 41

Thank you
