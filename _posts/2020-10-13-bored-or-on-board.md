---
layout: post
title: Bored, or on Board?
subtitle: Automated Feedback for Board Game Developers
cover-img: /assets/img/game-stack.png
thumbnail-img: /assets/img/game-stack.png
tags: [board games, NLP, ML]
---

Tabletop gaming is a big hobby of mine and millions of other people across the globe. Board games are a multi-billion dollar industry [1], with the number of games published each year growing exponentially since the early 2000s [2]. Games these days are no longer like Monopoly, which has the notorious reputation for being slow, dry, luck-based, and for causing fights due to made-up rules [3]. As the supply of board games increases, developers need their products to be vibrant, well-paced, and fun experiences in order to stand out.

![bg-per-year](https://i.imgur.com/pzuJRbm.png =75x)

### Project Introduction

Usually, a good selling game needs to be a good quality game. Central to a good quality game is constructive feedback and iteration. For my three week project at Insight Data Science, I built an application designed for board game developers who desire a quick way to get focused feedback for their board game prototypes. On the web-app, the developer can upload a csv file containing written reviews of their game and learn two things: 1. The overall sentiment about their game; 2. Sample sentences most likely to represent a particular issue raised in a board game review, such a mechanics or art direction. The developer can use this automated focus group to quickly parse how its perceived, common discussion points, and track how these metrics change over successive design cycles.

- Play around with the web-app: http://bit.ly/boredboardsite
- Find the project on my Github: https://github.com/josh-tollefson/bored-or-on-board

I built this web-app in Streamlit with the backend data acquisition, analysis, and machine learning (ML) modeling in Python. Board game comments and ratings were scraped from boardgamegeek.com with their XML-API [4]. In total, I processed ~300K comments and constructed a natural language processing (NLP) pipeline to prepare the comments for ML modeling. I then tackled two binary classification problems on the processed text: 1. Sentiment prediction (good or bad user rating); 2. Categorization prediction (does the review discuss the game’s mechanics? Art direction? Component quality?). Models for each are saved and used by the web-app to run on the developer-uploaded csv comment file. Each part of this pipeline is dissected in more detail below.

### Exploratory Data Analysis and NLP processing

Of the few hundred thousand scrapped comments, a significant fraction of them are short. I found that word length had an important impact on the quality of the model fits. Moreover, I was surprised by the number of comments that were written in a foreign language. I therefore tossed out comments that were below 25 words long and not in English.

![comment-length](https://i.imgur.com/tWqQ85w.png =100x)

I processed the comments by building an NLP pipeline with regex and NLTK. I removed capitalization and punctuation and ignored words that had little impact on meaning (stop words). Finally, I lemmatized words (shortening and consolidating words based on meaning and context) to avoid redundancy in my ultimate ‘board game review’ corpus. The final outcome of this NLP processing is illustrated in the below figure, which shows how individual words are parsed and combined. 

My library of processed comments was further divided into n-grams, with each n-gram encoded based on their document and comment frequency (TF-IDF). An n-gram is n sequential words in the text (e.g. “words in the text” is a 4-gram). This encoding results in a matrix of NLP-processed comments (rows) versus n-gram (columns), where each element is the frequency score of an n-gram for that comment. This matrix is the set of features fed into the binary prediction models. 

### Sentiment and Categorization Prediction

For sentiment prediction, I assigned each row (corresponding to a comment) a 0 or 1 label if the game corresponding to that comment was considered ‘bad’ or ‘good’, respectively. My definitions of good and bad lack nuance; I defined a ‘good’ game as one rated at least 9 out of 10 by the boardgamegeek.com comment writer, while a bad game is rated at most 4 out of 10. Games with ratings in between these ranges often contain ‘mixed signals’ in the text, making it difficult to classify the review as overtly positive or negative. I would love to incorporate more nuance into the sentiment classification for a comment by adding additional labels (such as ‘neutral’, ‘mediocre’, or ‘mixed’) or performing regression on the 10-point rating scale in the future.

Creating labels for the comment categorization was trickier as there lacked a structured way to group comments by content. One approach would be to ‘pseudo-label’ each comment by keywords. For instance, if a review contains the words ‘strategy’ and ‘hours’, it probably mentions the game’s mechanics and length. The obvious disadvantage is that if the review has none of the keywords, then it would not fit into any category. I also considered K-means clustering, but worried that the resulting clusters would not be easily interpreted. In the end, I decided to hand-label 1000 comments, labeling each comment by whether or not they discussed the following categories based on common points I’ve seen reading many board game reviews:

- Mechanics: Does the comment discuss how the game plays? 
- Art: Does the comment discuss art direction and theme?
- Components: Does the comment discuss component quality, legibility, or box size?
- Length: Does the comment mention how long it takes to complete the game?
- Accessibility: A catch-all for whether the comment discusses rule complexity, family-friendliness, price, and diversity.

For instance, the comment: “Monopoly is a dice rolling, family game that is overly long and luck-based” would be labeled [1,0,0,1,1] as it discusses the game’s mechanics, length, and accessibility (dice rolling, overly long, and family game), but not anything else. 

Having assigned labels to the encoded comments, I split the data into 80/20 training-test sets and ran Logistic Regression, Naive Bayes, and Random Forest binary classification models on the sentiment data and each of the category data. I achieved accuracies of ~85% for the sentiment analysis for each method. Precision was my metric of choice for comment categorization as I wanted to ensure that the sentences highlighted by my app were most likely to represent the label in question. I obtained precision of 80-90%, depending on the method and category. 

### Summary

“Bored, or on Board?” is a web-app designed to help board game developers get quick feedback for their game. In this three week sprint, I learned a lot about end-to-end project development in data science. If I had more time, I would extend the ideas developed in this project to other domains, such as video games via Steam reviews. In addition, I would love to incorporate additional data sources, such as Amazon and Kickstarter reviews and purchases, so that a developer using my app could determine how successive improvements in their game’s quality might result in improved sales. In this way, the developer could determine when their design is good enough, ultimately getting their product published faster and introducing more great games into the world!

- [1] https://www.statista.com/statistics/829285/global-board-games-market-value/
- [2] Figure made by me, with data collected from boardgamegeek.com
- [3] https://twitter.com/Calvinn_Hobbes/status/1087140198859722753/photo/1
- [4] Thanks to https://gitlab.com/recommend.games/board-game-scraper by Markus Shepherd