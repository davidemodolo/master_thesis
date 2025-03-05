> about 10s per row, 90 rows required

As we all know, in the recent years, AI has made enormous steps ahead.

This can be linked to different factors, but we can all agree that with the development of large language models (thanks to the transformer architecture), the entire industry exploded.

LLMs are trained on enormous amount of textual data and are able to generate human-like text with a correct grammar and syntax. Not only.

Thanks to the variety of the data used in the training process, these systems manifest emerging capabilities such as math skills, encryption understanding, planning, and more.

And here falls our project. Most of the literature tries to deploy LLM-based planning systems using fine tuning or various frameworks on top of the model.

With this work, we wanted to analyze the generative capabilities in planning intrinsic in LLMs.

We wanted to put to the test the planning capabilities of an LLM using only the text generation capabilities of the model.

An LLM inside an unknown environment performing a logistics task using the uncertainty.

I can't stress this enough, this is a generative approach and we want to see the intrinsic capabilities of an LLM in planning.

step-by-step

Uncertainty explanation - necessary step by step to compute uncertainty

Examples of planning where uncertainty could help (chess, delivery driver, etc.)

Our flow

Briefly on the environment: we run our test using the web-based platform Deliveroo.js developed by prof. Robol for the course Autonomous Software Agents.
This platform allows us to drive an agent in a 2D environment with parcels that spawn all around the map with the goal of picking them up and delivering them to a destination.

Models

Prompt Engineering

HM

Indeed we can se that if we let an agent run...

Qualitative results
GPT model comparison
Biases
