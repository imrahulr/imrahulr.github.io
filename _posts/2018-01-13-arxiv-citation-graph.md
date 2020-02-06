---
layout: post-right-sidebar
title:  Recreating arXiv Citation Graph using Recurrent Neural Networks
author: imrahulr
categories: [ RNNs, Deep Learning, Siamese Network ]
image: assets/images/14.jpg
description: A novel pipeline which employs a combination of traditional heuristics and modern deep learning methods to find the list of papers cited by an article.
github: https://git.ee.ethz.ch/rarade/arxiv-citation-graph
report: https://drive.google.com/file/d/1EZ6D9uqGEdv6fauDdBGo0D3S6QNd2ybZ/view?usp=sharing
---

> Semester Thesis at the Chair for Mathematical Information Science, ETH Zürich under Prof. Dr. Helmut Bölcskei.

## Oveview

The usefulness of a bibliographic database depends highly on a consistent citation graph which links the papers present in the database. Owing to the large variations in citation formats used for listing bibliographic information, it has become an increasingly difficult problem to accurately find the corresponding cited papers. In this work, we make an attempt towards recreating arXiv citation graph using only the raw LaTeX dump of research articles. We present a novel generalised pipeline which employs a combination of traditional heuristics and modern deep learning methods to find the list of papers cited by an article. The proposed 3-stage pipeline is illustrated in figure below.

<p align="center">
    <img src="{{ site.baseurl }}/assets/images/arxiv-citation/pipeline-1.png" alt="Overview of 3-stage pipeline"/>
</p>

We evaluate the results of the pipeline on the datasets provided in the data cleaning task of the KDD Cup 2003 which comprises a collection of over 35,000 articles submitted in the hep-ph (High Energy Physics - Phenomenology) section of arXiv between 1992 and 2003. Our pipeline extracts around 285k edges present in the hep-ph citation graph correctly out of 421k edges present in the true citation graph. This achieves an accuracy of around 95% while matching citations to the corresponding article and outperforms the top solutions presented in KDD Cup 2003 by a large margin with a 64% relative improvement in symmetric difference size. To validate the generality of the approach, we also report empirical results of the pipeline over another dataset presented in the same challenge for citation prediction task, which contains around 29,000 hep-th (High Energy Physics - Theory) papers submitted on arXiv. Detailed results can be found <a href="https://git.ee.ethz.ch/rarade/arxiv-citation-graph/blob/master/report_arxiv_final.pdf">here</a>.

---

## Important Links

<a href="https://www.cs.cornell.edu/projects/kddcup/">KDD Cup 2003</a><br>
<a href="https://www.cs.cornell.edu/projects/kddcup/download/KDDCup-Overview.pdf">KDD Cup Overview</a><br>
<a href="https://git.ee.ethz.ch/rarade/arxiv-citation-graph/blob/master/report_arxiv_final.pdf">Report</a><br>
<a href="https://arxiv.org/abs/1508.01991">Bidirectional LSTM-CRF Models for Sequence Tagging</a>