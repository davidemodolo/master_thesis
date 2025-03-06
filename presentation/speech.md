> about 10s per row, 90 rows required

As we all know, in the recent years, AI has made remarkable progress.

One of the most impactful advancements is the development of Large Language Models, that made a huge impact on the entire field of AI.

LLMs are trained on an enormous amount of textual data and are able to generate human-like text with a correct grammar and syntax. Not only.

Thanks to the variety of the data used in the training process, these systems manifest emerging behaviors such as mathematical reasoning and planning even though they were not explicitly trained for these tasks.

In this thesis, we ask: Can LLMs plan effectively without extra fine-tuning or specialized frameworks on top?".

Most LLM-based planning approaches available in the literature rely on additional frameworks, such as fine-
tuning, Chain-of-Thought prompting, or even hybrid reinforcement learning models.

These techniques aim to enhance planning by providing structured reasoning pathways.
But what happens if we strip everything away?

With this work, we wanted to analyze the generative capabilities in planning intrinsic in LLMs.

Our main research question is simple:
"Can an LLM, without additional training or frameworks, effectively plan and navigate in an unknown
environment?"
To explore this, we placed an LLM-driven agent inside a web-based game where it must pick up and
deliver parcels. This approach allows us to evaluate how well LLMs can make sequential decisions in real-time
environments.

I can't stress this enough, this is a generative approach and we want to see the intrinsic capabilities of an LLM in planning.

For our experiment we used the web-based platform Deliveroo.js developed by prof. Robol for the course Autonomous Software Agents. This platform allows us to drive an agent in a 2D environment with parcels that spawn all around the map with the goal of picking them up and delivering them to a destination.

A little before I said "evaluate how well LLMs can make sequential decisions". The keyword here is "sequential". Our approach relies on asking the model to give us only the next action to take given the current state of the environment, it's a "step-by-step" approach. This allows us to evaluate the model's uncertainty at any moment using a framework called KnowNo, introduced by Google.

This framework works like this: [KnowNo explanation]

A quick recap, the idea is like this: we have the agent in the game, we ask the model what to do next given the current state, we retrieve the uncertainty using KnowNo and we use this information to analyze the model's capabilities.

The data collected has been then used to build specialized heatmaps that show the model's uncertainty in every cell of the map. And the output is like this. We can see [explain the heatmap]

Not only, after that we computed for each cell the list of correct correct actions and created another heatmap that shows the model's accuracy in every cell of the map. The output is like this. We can see [explain the heatmap]

[Stateful and stateless comparison]

Indeed we can see that if we let an agent run we can see [problems ]

Prompt Strategy:
To ensure fair testing, we structured our prompts based on established literature.
We provided the LLM with:

1. A description of the current state of the environment.
2. A limited set of possible moves (up, down, left, right).
3. A request to select the best move, outputting only a single token.

Model choice

Qualitative results
GPT model comparison
Biases
