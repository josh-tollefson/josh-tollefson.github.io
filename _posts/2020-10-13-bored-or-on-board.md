---
layout: post
title: Bored, or on Board?
subtitle: Automated Feedback for Board Game Developers
cover-img: /assets/img/game-stack.png
thumbnail-img: /assets/img/game-stack.png
tags: [board games, NLP, ML]
---

Tabletop gaming is a big hobby of mine and millions of other people across the globe. According to Statista[1], tabletop games are a multi-billion dollar industry, with the number of games published each year growing exponentially since the early 2000s[2]. Games these days are no longer like Monopoly, which has the notorious reputation for being slow, dry, luck-based, and for causing fights due to made-up rules [3] (turns out, Free Parking does not mean free $$$). As the supply of board gaming swells, developers will more-and-more need their games to be vibrant, well-paced, and easy-to-learn, fun experiences in order to stand above the rising tide.

[Figure 1]

Usually, a good selling game needs to be a good quality game. Central to a good quality game is constructive feedback and iteration. For my project at Insight Data Science, I built an application designed for board game developers who desire a quick way to get focused feedback for their board game prototypes. On the web-app, the developer can upload a csv file containing written reviews of their game and learn two things: 1. The overall sentiment about their game; 2. Sample sentences most likely to represent a particular issue raised in a board game review, such a mechanics or art direction. The developer can use this automated focus group to quickly parse how its perceived, common discussion points, and track how these metrics change over successive design cycles.

[Figure 2 - Gif of webpage]

I built this web-app in Streamlit with the backend data acquisition, analysis, and machine learning (ML) modeling in Python. Board game comments and ratings were scraped from boardgamegeek.com with their XML-API (adapted from: ). In total, I processed ~300K comments and constructed a natural language processing (NLP) pipeline to prepare the comments for ML modeling. I then tackled two binary classification problems on the processed text: 1. Sentiment prediction (good or bad user rating); 2. Categorization prediction (does the review discuss the game’s mechanics? Art direction? Component quality?). Models for each are saved and used by the web-app to run on the developer-uploaded csv comment file. Each part of this pipeline is dissected in more detail below.

Exploratory Data Analysis and NLP processing

Of the few hundred thousand scrapped comments, a significant fraction of them are short. Word length has an important impact on the quality of the model fits (see next section). Moreover, I was surprised by the number of comments that were written in a foreign language. I therefore tossed out comments that were below 25 words long and not in English.

I processed the comments by building an NLP pipeline with regex and NLTK. I removed capitalization and punctuation 
