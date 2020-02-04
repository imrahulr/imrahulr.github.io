---
layout: post-right-sidebar
title:  "Toxic Comment Classification"
author: imrahulr
categories: [ NLP, Kaggle ]
image: assets/images/2.jpg
github: https://github.com/imrahulr/Toxic-Comment-Classification-Kaggle
---

>Deep learning to identify and classify toxic comments on online forums.

This post presents our solution for <a href="https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge">Toxic Comment Classification Challenge</a> hosted on Kaggle by Zigsaw. This solution ranked 15th on the private leaderboard. The code can be found in <a href="https://github.com/imrahulr/Toxic-Comment-Classification-Kaggle">this</a> GitHub repository.

---

## Identify and Classify Toxic Online Comments

Discussing things you care about can be difficult. The threat of abuse and harassment online means that many people stop expressing themselves and give up on seeking different opinions. Platforms struggle to effectively facilitate conversations, leading many communities to limit or completely shut down user comments.

So, in this competition on Kaggle, the challenge was to build a multi-headed model that’s capable of detecting different types of of toxicity like threats, obscenity, insults, and identity-based hate better than Perspective’s current models. A dataset of comments from Wikipedia’s talk page edits was provided. 

---

## Data Overview

The dataset used was Wikipedia corpus dataset which was rated by human raters for toxicity. The corpus contains comments from discussions relating to user pages and articles dating from 2004-2015.

The comments are to be tagged in the following six categories - 
<ul>
    <li>toxic</li>
    <li>severe_toxic</li>
    <li>obscene</li>
    <li>threat</li>
    <li>insult</li>
    <li>identity_hate</li>
</ul>

---

### Train and Test Data

The training data contains a row per comment, with an id, the text of the comment, and 6 different labels that we have to predict.

```python
import pandas as pd
import numpy as np

train_df = pd.read_csv('train.csv')
test_df = pd.read_csv('test.csv')
print('Train shape: ', train_df.shape)
print('Test shape: ', test_df.shape) 
```

```
Train shape:  (159571, 8)
Test shape:  (153164, 2)
```

The training dataset is highly imbalanced with about 1,43,346 clean comments and only 16,225 comments with toxicity as shown below - .

<p align="center">
<img src="{{ site.baseurl }}/assets/images/toxic/noofoccurrences.png" alt="Number of Occurrences of each Class"/>
</p>

---

## Train Data after Basic Preprocessing and Cleaning

<br>

| ID | Comment Text |
|:--:|--------------|
| 0 |	explanation why the edits made under my userna... |
| 1 |	d aww ! he matches this background colour i am... |
| 2 |	hey man i am really not trying to edit war it ... |
| 3 |	more i cannot make any real suggestions on im...  |
| 4 |	you sir are my hero any chance you remember wh... |
{:class="table table-bordered"}

---

## Test Data after Basic Preprocessing and Cleaning

<br>

| ID | Comment Text |
|:--:|-------|
| 0 | 	yo bitch ja rule is more succesful then you wi... |
| 1 | 	= = from rfc = = the title is fine as it is imo |
| 2 | 	= = sources = = zawe ashton on lapland |
| 3 | 	if you have a look back at the source the inf... |
| 4 | 	i do not anonymously edit articles at all |
{:class="table table-bordered"}

---

## Cleaning Data

Pre-processing includes removing special symbols and punctuations from comments, converting to upper-case characters to lower case. The preprocessed comments were converted to fixed-length sequences by either truncating or padding with zeros. We can also perform stemming and lemmatization on the comments, but they are not effective when using deep learning architectures.

```python
def cleanData(text, stemming=False, lemmatize=False):    
    text = text.lower().split()
    text = " ".join(text)
    text = re.sub(r"[^A-Za-z0-9^,!.\/'+\-=]", " ", text)
    text = re.sub(r"what's", "what is ", text)
    text = re.sub(r"\'s", " ", text)
    text = re.sub(r"\'ve", " have ", text)
    text = re.sub(r"can't", "cannot ", text)
    text = re.sub(r"n't", " not ", text)
    text = re.sub(r"i'm", "i am ", text)
    text = re.sub(r"\'re", " are ", text)
    text = re.sub(r"\'d", " would ", text)
    text = re.sub(r"\'ll", " will ", text)
    text = re.sub(r",", " ", text)
    text = re.sub(r"\.", " ", text)
    text = re.sub(r"!", " ! ", text)
    text = re.sub(r"\/", " ", text)
    text = re.sub(r"\^", " ^ ", text)
    text = re.sub(r"\+", " + ", text)
    text = re.sub(r"\-", " - ", text)
    text = re.sub(r"\=", " = ", text)
    text = re.sub(r"'", " ", text)
    text = re.sub(r"(\d+)(k)", r"\g<1>000", text)
    text = re.sub(r":", " : ", text)
    text = re.sub(r" e g ", " eg ", text)
    text = re.sub(r" b g ", " bg ", text)
    text = re.sub(r" u s ", " american ", text)
    text = re.sub(r"\0s", "0", text)
    text = re.sub(r" 9 11 ", "911", text)
    text = re.sub(r"e - mail", "email", text)
    text = re.sub(r"j k", "jk", text)
    text = re.sub(r"\s{2,}", " ", text)
    if stemming:
        st = PorterStemmer()
        txt = " ".join([st.stem(w) for w in text.split()])
    if lemmatize:
        wordnet_lemmatizer = WordNetLemmatizer()
        txt = " ".join([wordnet_lemmatizer.lemmatize(w) for w in text.split()])
    return text
```

---

## Exploring Train Data

#### Correlation between Output Classes
<p align="center">
<img src="{{ site.baseurl }}/assets/images/toxic/corr.png" alt="Correlation between Output Classes"/>   </p>

#### Words frequently occurring in Toxic Comments
<p align="center">
<img src="{{ site.baseurl }}/assets/images/toxic/wordtoxic.png" alt="Words frequently occurring in Toxic Comments"/>
</p>
                                                                                                      
#### Words frequently occurring in Severe Toxic Comments
<p align="center">
<img src="{{ site.baseurl }}/assets/images/toxic/wordstox.png" alt="Words frequently occurring in Severe Toxic Comments"/>
</p>

#### Words frequently occurring in Threat Comments
<p align="center">
<img src="{{ site.baseurl }}/assets/images/toxic/woedthreat.png" alt="Words frequently occurring in Threat Comments"/>
</p>

#### Words frequently occurring in Insult Comments
<p align="center">
<img src="{{ site.baseurl }}/assets/images/toxic/wordinsult.png" alt="Words frequently occurring in Insult Comments"/>
</p>

---

## Our Solution


The final solution consists of ensemble of several machine learning models - 

<ul>
<li>Attention with Bidirectional LSTM</li>
<li>Bidirectional LSTM with Pre-Post Input Text</li>
<li>Bidirectional GRU with derived features</li>
<li>Capsule Network</li>    
<li>CNN based on DeepMoji Architecture</li>
<li>CNN + GRU</li>
<li>DeepMoji Architecture</li>
<li>Character Level Hierarchical Network</li>
<li>Ensemble of Logistic Regression and SVM</li>
<li>2D CNN</li>
<li>LightGBM</li>
</ul>

Each model was trained using 10 fold validation with proper hyperparameter tuning. Each model was fed with starting 150 words along with 150 words from the end of the comments. This provided a significant boost in the performance since many comments were toxic at the end.

Finally, we used LightGBM, Catboost and simple weighted averaging for stacking these models, thus creating a three-layer ensemble of models.

---

## Embeddings Used

Various pre-trained embeddings were used to create diverse models -
<ul>
    <li>GloVe</li>
    <li>fastText</li>
    <li>word2vec</li>
    <li>Byte-Pair Encoded subword embeddings (BPE)</li>
</ul> 

---

## Results

<ul>
<li>The overall model got a ROC AUC score of 0.9874 on private LB.</li>
<li>Preprocessing was not much impactful and did not significantly improve the score of any model.</li>
<li>RNN models were significantly better than CNN models.</li>
<li>The best model was <a href="#">recurrent capsule network</a> followed by DeepMoji and CNN-GRU.</li>
<li>Adding attention layer to RNN models boosted their score.</li>
<li>Logistic regression and LightGBM models had much lower scores but provided diversity.</li>
<li>Some input samples were toxic only in the last few words. Thus, training each model on ending 150 words in addition to starting 150 words improved their performance.</li>
</ul>

## Thank You!

---

## Important Links

<a href="https://github.com/imrahulr/Toxic-Comment-Classification-Kaggle">GitHub Repository</a><br>
<a href="https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge">Toxic Comment Classification Challenge - Kaggle</a><br>
<a href="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjWg-LowaPbAhVGOI8KHb1RAfUQFggtMAA&url=https%3A%2F%2Fwww.ijcai.org%2FProceedings%2F16%2FPapers%2F408.pdf&usg=AOvVaw3csL-yYL5hsvDLyqjPHBcm">RNN for Text Classification</a><br>
<a href="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwjWg-LowaPbAhVGOI8KHb1RAfUQFgg-MAE&url=http%3A%2F%2Funivagora.ro%2Fjour%2Findex.php%2Fijccc%2Farticle%2Fdownload%2F3142%2Fpdf&usg=AOvVaw02sgX96hAwVlOEbe14etjm">Attention based RNN</a><br>
<a href="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=5&cad=rja&uact=8&ved=0ahUKEwjWg-LowaPbAhVGOI8KHb1RAfUQFghaMAQ&url=https%3A%2F%2Fwww.aaai.org%2Focs%2Findex.php%2FAAAI%2FAAAI15%2Fpaper%2Fdownload%2F9745%2F9552&usg=AOvVaw37k05lV8569fo_aCghlO9i">RNN + CNN</a><br>
<a href="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=9&cad=rja&uact=8&ved=0ahUKEwjJh7S8wqPbAhUM6Y8KHU1hBlgQFgiYATAI&url=http%3A%2F%2Fwww.aclweb.org%2Fanthology%2FE17-1104&usg=AOvVaw0JI4qVgzT-D0RbCnQIfQtS">Very Deep CNN</a><br>
<a href="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwiVgprRwqPbAhVHv48KHYkEAm4QFggtMAA&url=http%3A%2F%2Fai.tencent.com%2Failab%2Fmedia%2Fpublications%2FACL3-Brady.pdf&usg=AOvVaw2HPvD4WxrxM1-4-rK3jUa1">Deep Pyramid CNN</a><br>
<a href="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjZx-DxwqPbAhXEvo8KHVRWCUQQFggtMAA&url=http%3A%2F%2Fwww.cs.cmu.edu%2F~.%2Fhovy%2Fpapers%2F16HLT-hierarchical-attention-networks.pdf&usg=AOvVaw0NlQCca0WmnRw5Q6SczY-b">Hierarchical Attention Network</a><br>
<a href="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwi6yNXFw6PbAhWIrI8KHfvuDFcQFggtMAA&url=https%3A%2F%2Fnlp.stanford.edu%2Fpubs%2Fglove.pdf&usg=AOvVaw3XPTcwWcbYOXnahjvpeDTu">GloVe</a><br>
<a href="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwi7zezUw6PbAhWKYo8KHaGeCbAQFggoMAA&url=https%3A%2F%2Fgithub.com%2Ffacebookresearch%2FfastText&usg=AOvVaw1e2FnEQ3qGQ2tLYuDOSFJn">fastText</a><br>
<a href="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwih_vTaw6PbAhUHSY8KHTy0AeAQFggoMAA&url=https%3A%2F%2Fgithub.com%2Fbheinzerling%2Fbpemb&usg=AOvVaw1_Fi25NN0bQjS8u-2Pg_gK">BPE</a><br>
<a href="https://www.kaggle.com/jagangupta/stop-the-s-toxic-comments-eda">EDA</a><br>
<a href="https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge/discussion/52563#latest-300126">Summary of our solution by Mohsin</a>

