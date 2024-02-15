---
title: PyTorch zero_grad()
type: page
description: ML
topic: career
---

### U-Net Segmentation

When I was training a U-Net for Brain Tumour Segmentation, I encountered an issue where the training losses were jumping around randomly. This was combined withnot identifying segmented pixels when visualising the performance of my model on test data.  

To diagnose the issue, I (with the help of a GTA) took a look at the part in which I trained my model in PyTorch. Turns out, this issue was because I did not use `optimizer.zero_grad()`.  

### Why Zero Gradients?

`.zero_grad()` sets the gradients of all model parameters to zero. We need to do this because PyTorch accumnmulates the gradients on subsequent backward passes during backpropagation. This is convenient when we want to sum gradients over multiple mini-batches, and is the default behaviour. If we do not zero out the gradients, our gradients will have the old gradient combined, and thus will not move towards the minimum.  

A useful [StackOverflow Post](https://stackoverflow.com/questions/48001598/why-do-we-need-to-call-zero-grad-in-pytorch).
