# Recurrent Neural Network Regularization

Type: Paper
Link: https://arxiv.org/pdf/1409.2329#page=2.01

Paper about why straightforward usage of Dropout doesn’t work well with RNNs (LTMs), and what to do to get a better generalisation.

In short: Because dropout corrupts the carried information, the idea is to force the units to perform their intermediate computations more robustly without erasing information. To do so, they apply dropout only to the non-recurrent connections.

![Untitled](RECURRENT%20NEURAL%20NETWORK%20REGULARIZATION%20e83b1851ab7945e7a9fee0f37e566677/Untitled.png)
<div style="display: flex; justify-content: space-between;">
    <img src="RECURRENT%20NEURAL%20NETWORK%20REGULARIZATION%20e83b1851ab7945e7a9fee0f37e566677/Untitled%201.png" alt="Image 1" style="width: 45%;"/>
    <img src="RECURRENT%20NEURAL%20NETWORK%20REGULARIZATION%20e83b1851ab7945e7a9fee0f37e566677/Untitled%202.png" alt="Image 2" style="width: 45%;"/>
</div>


Note:
- Practical applications of RNNs often used models that are too small because large RNNs tend to overfit.

Related:
- Dropout improves Recurrent Neural Networks for Handwriting Recognition, V. Pham et al., 2013
- Dropout: A Simple Way to Prevent Neural Networks from Overfitting, N. Srivastava et al., 2014