# Accelerating Machine Learning with MLOps and FuseML - Part 1

## Foreword

Building successful Machine Learning production systems requires a specialized re-interpretation of the traditional DevOps culture and methodologies.
MLOps, short for Machine Learning Operations, is a relatively new engineering discipline and a set of practices meant to improve the collaboration and communication between the various roles and teams that together manage the end-to-end lifecycle of Machine Learning projects.

Helping enterprises adapt and succeed with Open Source is one of SUSE's key strengths. At SUSE, we have the experience to understand the difficulties posed by adopting disrupting technologies and to accellerate the digital transformation. Machine Learning and MLOps are no different.

The SUSE AI/ML team has recently launched [FuseML](https://fuseml.github.io), an Open Source orchestration framework for MLOps. FuseML brings a novel holistic interpretation of MLOps advocated practices, to help organizations reshape the lifecycle of their Machine Learning projects by facilitating frictionless interaction between all roles involved in Machine Learning development while avoiding massive operational changes and vendor lock-in.

This is the first in a series of articles that provides a gradual introduction into Machine Learning, MLOps and the FuseML project. We start here by rediscovering some basic facts about Machine Learning and why it is a technology so fundamentally different than traditional software. In the next articles, we take a closer look at some of the key MLOps findings and recommendations and how we interpret and incorporate them into the FuseML project principles.

## MLOps Overview

Old habits that need changing can be difficult to unlearn, even more difficult than re-learning everything anew. It's true for people and it's even truer for teams and organizations, where the combined inertia that makes important changes difficult to implement is several orders of magnitude greater.  

With the AI hype on the rise, organizations have been investing more and more in Machine Learning to make better and faster business decisions or to automate key aspects of their operations and production processes. But if history taught us anything about adopting disrupting software technologies like virtualization, containerization and cloud computing, it's that getting results doesn't happen over night and it often requires significant operational and cultural changes. With Machine Learning, this challenge is very pronounced, with more than 80% of AI projects failing to deliver business outcomes, as reported by Gartner in 2019 and repeatedly confirmed by business analysts and industry leaders throughout 2020 and 2021.

Naturally, following this realization about the challenges of using Machine Learning in production, a lot of effort went into investigating the whys and whats about this state of affairs. Today, the main causes for this phenomenon are better understood and a brand new engineering discipline - MLOps - was created to tackle the specific problems that Machine Learning systems encounter in production. 

The recommendations and best practices assembled under the MLOps label are rooted in the recognition that Machine Learning systems have specialized requirements that demand changes in the development and operational project lifecycle as well as in organizational culture. MLOps doesn't propose to reinvent how we do DevOps with software projects. It's still DevOps, but pragmatically applied to Machine Learning.

MLOps ideas can be traced back all the way to the defining characteristics of Machine Learning. The remainder of this article is focused on revisiting what differentiates Machine Learning from conventional programming. We'll use the fundamental insights revealed in this exercise as stepping stones when we dive deeper into MLOps in the next chapter of this series.

## Machine Learning Characteristics

Solving a problem with traditional programming requires a human agent to formulate a solution, usually in the form of one or more algorithms, and then translate it into a set of explicit instructions that the computer can execute efficiently and reliably. Generally speaking, conventional programs, when correctly developed, are expected to give accurate results and to have highly predictable and easily reproducible behaviors. When a program produces an erroneous result, we treat that as a defect that needs to be reproduced and fixed. As a best practice, we also process conventional software through as much testing as possible _before_ deploying it in production, where the business cost incurred for a defect could be substantial. We rely on the results of proactive testing to give us some guarantees about how the program will behave in the future, another characteristic derived from the predictibility aspect of conventional software. As a result, once released, a software product is expected to take significantly less effort to maintain compared to development.

Some of these statements are highly generic, one might say they could even be used to describe products in general, software or otherwise. What they all have in common though is they no longer hold as valid when applied to Machine Learning.

Machine Learning algorithms are distinguished by their ability to learn from experience (i.e. from patterns in input data) to behave in a desired way, rather than being programmed to do so through explicit instructions. Human interaction is only required during the so called _training phase_, when the ML algorithm is carefully calibrated and data is fed into it, resulting in a _trained_ program, also called a _ML model_. With proper automation in place, it may even seem that human interaction could be elliminated completely, but as we'll see later in this post, it's just that the human responsibilities shift from programming to other activities, such as data collection and processing and ML algorithm selection, tuning and monitoring.

Machine Learning can be used to solve a specific class of problems:
* the problem is extremely difficult to solve mathematically or programmatically or has only solutions that are too computationally expensive to be practical 
* a fair amount of data exists (or can be generated), containing a pattern that a ML algorithm can learn

Let's look at two examples, similar, but situated at opposite ends of the spectrum as far as utility is concerned.

### Example One: Sum of Two Numbers

A very simple example, albeit with no practical application whatsoever, is training a ML model to be able to calculate the sum of two real numbers. Doing this with conventional programming is trivial and always yields very accurate results. Training a ML model for the same task could be summarized as follows:

* first, we need to prepare the input data that will be used to train the ML model. Generally speaking, training data is structured as a set of entries, where each entry associates a concrete set of values used as input for the target problem with the correct answer (also known as a _target_ or _label_ in ML terms). In our example, each entry maps a pair of input real values (X, Y) to the desired result (X+Y) that we expect the model to learn to compute. For this purpose, we can generate the training data entirely using conventional programming, but it's often the case with Machine Learning that training data is not readily available and can be expensive to acquire and prepare. The code used to generate the input dataset could look like this:

```
import numpy as np
train_data = np.array([[1.0,1.0]])
train_targets = np.array([2.0])
for i in range(3,10000,2):
    train_data = np.append(train_data,[[i,i]],axis=0)
    train_targets = np.append(train_targets,[i+i])
```

Deciding what kind of data is needed, how much of it and how it needs to be structured and labeled to yield acceptable results during ML training is the realm of Data Science. The data collection and preparation phase is critical to ensuring the success of ML projects. It takes experimentation as well as experience to find out which approach yields the best result and Data Scientists often need to iterate several times through this phase and improve the quality of their training data to raise the accuracy of ML models.


* next, we need to define the ML algorithm and train it (also known as _fitting_) on the input data. For our goal, we can use an Artificial Neural Network (ANN) suitable for this type of problem (regression). The code for it could look like this:


```
import tensorflow as tf
from tensorflow import keras
import numpy as np

model = keras.Sequential([
    keras.layers.Flatten(input_shape=(2,)),
    keras.layers.Dense(20, activation=tf.nn.relu),
    keras.layers.Dense(20, activation=tf.nn.relu),
    keras.layers.Dense(1)
])

model.compile(optimizer='adam', 
              loss='mse',
              metrics=['mae'])

model.fit(train_data, train_targets, epochs=10, batch_size=1)
```

Similar to data preparation, deciding which ML algorithm to use and what values should be configured for its parameters for best results (e.g. the neural network architecture, optimizer, loss, epochs) requires specific ML knowledge and iterative experimentation. However, by now, ML as a technology is mature enough to make finding an algorithm that fits the problem not difficult, especially given that there are countless open source libraries, examples, ready-to-use ML models and documented use-case patterns and recipes available for all major classes of problems that can be solved with ML, that one can start from. Moreover, many of the decisions and activities required to develop a performant ML model (e.g. hyperparameter tuning, neural architecture search) can already be fully automated or accelerated through partial automation by means of a special category of tools called _AutoML_.

* we now have a trained ML model that we can use to calculate the sum of any two numbers (i.e. make predictions):

```
def sum(x, y):
  s = model.predict([[x, y]])[0][0]
  print("%f + %f = %f" % (x, y, s))
```

The first thing to note is that the summation results produced by the trained model are not at all accurate. In fact, it's fair to say that the ML model is not behaving like it's calculating the result, but more like it's giving a ballpark estimation of what the result might be, as showed in this set of examples:

```
# sum(2000, 3000)
2000.000000 + 3000.000000 = 4857.666992
# sum(4, 5)
4.000000 + 5.000000 = 9.347977
```

Another notable characteristic is, as we move further away from the pattern of values on which the model was trained, the model's predictions get worse. In other words, the model is better at estimating summation results for input values that are more similar to the examples on which it was trained:

```
# sum(10, 10000)
10.000000 + 10000.000000 = 8958.944336
# sum(1000000, 4)
1000000.000000 + 4.000000 = 1318969.375000
# sum(4, 1000000)
4.000000 + 1000000.000000 = 895098.750000
# sum(0.1, 0.1)
0.100000 + 0.100000 = 0.724608
# sum(0.01, 0.01)
0.010000 + 0.010000 = 0.549576
```

This phenomenon is well known to ML engineers. If not properly understood and addressed, it can lead to ML specific problems that take various forms and names:
* _bias_: using incomplete, faulty or prejudicial data to train ML models that end up producing biased results
* _training-serving skew_: training a ML model on a dataset that is not representative of the real world conditions in which the ML model will be used
* _data drift_, _concept drift_ or _model decay_: the degradation, in time, of the model quality, as the real world data used for predictions changes to the point where the initial assumptions on which the ML model was trained are no longer valid

In our case, it's easy to see that the model is performing poorly due to a skew situation: we inadvertently trained the model on pairs of _equal_ numbers, which is not representative of the real world conditions in which we want to use it. Our model also completely missed the point that addition is commutative, but that's not surprising, given that we didn't use training data representative of this property either.

When developing ML models to solve complex, real-world problems, detecting and fixing this type of problems is rarely that simple. Machine Learning is as much an art as it is a science and engineering endeavour.

In training ML models, there is usually also a validation step involved, where the labeled input data is split and part of it is used to test the trained model and calculate its accuracy. This step is intentionally omitted here, for the sake of simplicity. The full exercise of implementing this example, with complete code and detailed explanations is covered in [this article](https://www.pluralsight.com/guides/deep-learning-model-add). 

### Example Two: the Three-Body Problem

At the other end of the spectrum is a physics (classical mechanics) problem that has inspired one of the greatest mathematicians of all times, Isaac Newton, to literally invent an entirely new branch of math and nowadays a source of constant frustration among high school students: Calculus.

Finding the solution to the set of equations that describe the motion of two celestial bodies (e.g. the Earth and the Moon) given their initial positions and velocities is already a complicated problem. Extending the problem to include a third body (e.g. the Sun) complicates things to the point where a solution cannot be found and the entire system starts behaving chaotically. With no mathematical solution in sight, Newton himself felt that supernatural powers had to be at play to account for the apparent stability of our solar system.

This problem and its generalized form, the many-body problem, are so famous because solving them is a fundamental part of space travel, space exploration, cosmology and astrophysics. Partial solutions can be calculated using analytical and numerical methods, but it requires immense computational power.

All life forms on this planet are constantly used to dealing with gravity. We are well equipped to learn from experience and we're able to make pretty accurate predictions regarding its effects on our bodies and on the objects we interact with. It is therefore not entirely surprising that Machine Learning can be used to estimate the motion of objects under the effect of gravity. 

Using Machine Learning, researchers at the University of Edinburgh have been able to train a ML model that is capable of solving the three-body problem _100 million times faster_ compared to traditional means. The full story covering this achievement is available [here](https://www.technologyreview.com/2019/10/26/132171/a-neural-net-solves-the-three-body-problem-100-million-times-faster/) and the original paper can be read [here](https://arxiv.org/pdf/1910.07291.pdf).

Solving the three-body problem with ML is very similar to our earlier trivial example of adding two numbers together. The training and validation datasets are also generated through simulation and an ANN is also involved here, albeit one with a more complex structure. The main differences are the complexity of the problem and the immediate practical application that ML brings to this use-case. However, the observations previously stated about general ML characteristics apply equally to both cases, regardless of complexity and utility.

### Conclusions

We haven't even begun to look at MLOps in detail, but we can already identify and summarize key takeaways representative of ML in general just by comparing classical programming to Machine Learning:

1. not all problems are good candidates for Machine Learning. In the addition example, ML brings absolutely no advantage compared to conventional programming. Deciding whether Machine Learning is the right approach for a problem should be done by carefully analyzing the advantages and disadvantages that ML has compared to standard programming.

2. the process of developing ML models is iterative, explorative and experimental in nature. It often takes a lot of iterations to correctly prepare the data and to calibrate the ML model parameters to yield satisfactory results. There's experimentation involved in conventional programming too, but to a far less extent, whereas with ML this is more the rule than the exception. Experimentation also doesn't have to stop when a ML model is deployed in production, it's an ongoing process required to ensure that the ML model continues to perform acceptably as the world around it changes. 

3. developing a Machine Learning system requires dealing with new categories of artifacts with specialized behaviours that don't fit the patterns of conventional software. Datasets and ML models are first class concepts in the ML practice. 

4. it's usually not possible to produce fully accurate results with ML models. In fact, getting 100% accuracy for a ML model during testing is often a sign that something might have gone wrong (e.g. overfitting or test data tempering). The probabilistic and statistical nature of Machine Learning behaviors and results is something that the traditional software engineer isn't well equipped to understand and deal with. In contrast with traditional software, there's always a chance that a ML model might generate incorrect results, and there's no single recipe for deciding whether or not this is acceptable and to what degree.

5. developing and working with Machine Learning based systems requires a specialized set of skills, in addition to those needed for traditional software engineering. Data Scientists and ML engineers are actors that play a key role especially in more complex AI/ML projects. One fact about ML that is not immediately obvious is that these actors cannot function in a siloed manner and just hand some artifacts over to the person responsible for the next stage in the development lifecycle of a ML system. They need to collaborate closely and have a shared understanding about basic Data Science and Machine Learning concepts, as well as the particular strategy being used for the project.

6. running ML systems in the real world is far less predictable than what we're used to with regular software. Especially in cases where the data used to make predictions changes dynamically with time, pre-deployment testing can only give limited guarantees that the ML model will yield expected results in production. The techniques of reproducing, investigating and fixing a defective ML model are completely different than those used in traditional software engineering. A classical bug can often be traced back to a faulty line of code that can be easily patched and released as an update. Understanding the causes behind an inaccurate set of ML predictions involves the use of specialized tools and techniques and can be as laborious as developing a ML model from scratch.

7. finally, developing ML systems would be next to impossible without specialized tools. So far we've encountered NumPy, TensorFlow and Keras, but that only barely scratches the surface of what is otherwise an overwhelming ecosystem of libraries, frameworks and platforms that continues to grow with no end in sight. Making sense of it all can be highly rewarding, but equally exhausting.

Machine Learning characteristics summarized here are reflected in the MLOps discipline and distilled in the principles on which we based the FuseML orchestration framework project. The next article in the series will give a detailed account of MLOps recommendations and how an MLOps orchestration framework like FuseML can make developing and operating ML systems an automated and frictionless experience.

Does your experience or expectations of Machine Learning match the ideas presented in this article ? Regardless of answer, I'm really looking forward to hearing your opinion. Let me know what you think in the comments section.