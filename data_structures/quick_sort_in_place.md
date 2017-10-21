# ARTICLE IN PROGRESS, PLEASE VISIT [HOME][home] FOR FINISHED ARTICLES

# Quick Sort in Place (sub-linear space complexity)

This article aims to show the reader how to construct quick sort algorithm that uses sub-linear space and has logarithmic time complexity. Note that the worst case for time complexity is still O(n**2), but this approach minimizes the probability of the worst case.

## Overview

Quick Sort algorithm can be summarized in 3 simple steps:
* Select a pivot point.
* Everything less than the pivot point goes to the left. Everything else goes to the right.
* Repeat the process for the left side and the right side

[Home][home]

[home]: ../README.md
