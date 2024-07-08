# Keeping Neural Networks Simple by Minimizing the Description Length of the Weights

Type: Paper
Link: https://www.cs.toronto.edu/~hinton/absps/colt93.pdf

# Keeping Neural Networks Simple by Minimizing the Description Length of the Weights

Paper goal: Find the simplest model (least information In its weights) that achieves good performance (+ Generalisation).

Paper key elements:

- MDL principle: A good model should be able to describe the data with the fewest nb of bits: Minimises the combined cost of describing the model (i.e. nb bits to describe the model) and describing the misfit between the model and the data (i.e. nb bits to describe the discrepancy between the correct and predicted output).
- By minimising (MDL) the encoding the data misfit (discrepancy between the model's predictions and the actual observed data) + Code length (description length of the model) the MDL argues that the model will be good
- They add noise (hidden layer) in order to reduce the precision of the model: lower accuracy -> requires fewer bits for each weight (increase the entropy and thus reduce the nb of bits), and better generalisation
- Use the data to determine the prior like using an adaptive mixture of Gaussians instead of a single gaussian . Means, variances and mixing proportions in the mixture adapt to model the clusters in the weight values.
- Results:
    - Few training cases and 4 times more tests to test the effectiveness of the model’s generalisation
    - prior: Mixture of 5 gaussians
    - Posterior: Gaussian(0,0.1)
    - Better results when penalising the weights (+ bias): shows that they can fit a complicated model with number of training cases inferior that dimensions in the input vector (and can be better than just simple weight-decay).