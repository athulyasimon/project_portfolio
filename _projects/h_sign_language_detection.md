---
layout: project
title: Sign Language Detection
date: March 13, 2015
image: https://raw.githubusercontent.com/athulyasimon/project_portfolio/gh-pages/public/images/sign_language.jpg
---

## Overview
Sign Language Detection

The goal of this project was to distinguish between images of a few letters of the signed alphabet. In order to do this we started with feature extraction. Here we removed the background from the image based on a color threshold, found the edges of the hand by using a Sobel edge detector, and determined which lines were most significant through a Hough Transform. Next came feature selection where we compared all the feature combinations for each letter to see which ones were the best for seperating the data. After determining which features to select we used a soft-margin support vector machine to identify a classifier. We then used the classifier to see how often we correctly identified the letters E, H, L, O, and Y. We correctly identified all these letters above 65% of the time and the letter H was correctly identfied above 95% of the time. This [paper](https://raw.githubusercontent.com/athulyasimon/project_portfolio/gh-pages/public/papers/SignLanguageRecognition.pdf) and the following video further explain the project. 

<iframe src="https://www.youtube.com/watch?v=a2peO_Kktho" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

[![Sign Language Detection](http://img.youtube.com/vi/a2peO_Kktho/1.jpg)](http://www.youtube.com/watch?v=a2peO_Kktho "ASL Report")


