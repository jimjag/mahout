<!--
 Licensed to the Apache Software Foundation (ASF) under one or more
 contributor license agreements.  See the NOTICE file distributed with
 this work for additional information regarding copyright ownership.
 The ASF licenses this file to You under the Apache License, Version 2.0
 (the "License"); you may not use this file except in compliance with
 the License.  You may obtain a copy of the License at

     http://www.apache.org/licenses/LICENSE-2.0

 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.
-->
---
layout: doc-page
title: (Deprecated)  Hidden Markov Models

    
---

# Hidden Markov Models

<a name="HiddenMarkovModels-IntroductionandUsage"></a>
## Introduction and Usage

Hidden Markov Models are used in multiple areas of Machine Learning, such
as speech recognition, handwritten letter recognition or natural language
processing. 

<a name="HiddenMarkovModels-FormalDefinition"></a>
## Formal Definition

A Hidden Markov Model (HMM) is a statistical model of a process consisting
of two (in our case discrete) random variables O and Y, which change their
state sequentially. The variable Y with states \{y_1, ... , y_n\} is called
the "hidden variable", since its state is not directly observable. The
state of Y changes sequentially with a so called - in our case first-order
- Markov Property. This means, that the state change probability of Y only
depends on its current state and does not change in time. Formally we
write: P(Y(t+1)=y_i|Y(0)...Y(t)) = P(Y(t+1)=y_i|Y(t)) = P(Y(2)=y_i|Y(1)).
The variable O with states \{o_1, ... , o_m\} is called the "observable
variable", since its state can be directly observed. O does not have a
Markov Property, but its state probability depends statically on the
current state of Y.

Formally, an HMM is defined as a tuple M=(n,m,P,A,B), where n is the number of hidden states, m is the number of observable states, P is an n-dimensional vector containing initial hidden state probabilities, A is the nxn-dimensional "transition matrix" containing the transition probabilities such that A\[i,j\](i,j\.html)
=P(Y(t)=y_i|Y(t-1)=y_j) and B is the mxn-dimensional "emission matrix"
containing the observation probabilities such that B\[i,j\]=
P(O=o_i|Y=y_j).

<a name="HiddenMarkovModels-Problems"></a>
## Problems

Rabiner \[1\](1\.html)
 defined three main problems for HMM models:

1. Evaluation: Given a sequence O of observations and a model M, what is
the probability P(O|M) that sequence O was generated by model M. The
Evaluation problem can be efficiently solved using the Forward algorithm
2. Decoding: Given a sequence O of observations and a model M, what is
the most likely sequence Y*=argmax(Y) P(O|M,Y) of hidden variables to
generate this sequence. The Decoding problem can be efficiently solved
using the Viterbi algorithm.
3. Learning: Given a sequence O of observations, what is the most likely
model M*=argmax(M)P(O|M) to generate this sequence. The Learning problem
can be efficiently solved using the Baum-Welch algorithm.

<a name="HiddenMarkovModels-Example"></a>
## Example

To build a Hidden Markov Model and use it to build some predictions, try a simple example like this:

Create an input file to train the model.  Here we have a sequence drawn from the set of states 0, 1, 2, and 3, separated by space characters.

    $ echo "0 1 2 2 2 1 1 0 0 3 3 3 2 1 2 1 1 1 1 2 2 2 0 0 0 0 0 0 2 2 2 0 0 0 0 0 0 2 2 2 3 3 3 3 3 3 2 3 2 3 2 3 2 1 3 0 0 0 1 0 1 0 2 1 2 1 2 1 2 3 3 3 3 2 2 3 2 1 1 0" > hmm-input

Now run the baumwelch job to train your model, after first setting MAHOUT_LOCAL to true, to use your local file system.

    $ export MAHOUT_LOCAL=true
    $ $MAHOUT_HOME/bin/mahout baumwelch -i hmm-input -o hmm-model -nh 3 -no 4 -e .0001 -m 1000

Output like the following should appear in the console.

    Initial probabilities: 
    0 1 2 
    1.0 0.0 3.5659361683006626E-251 
    Transition matrix:
      0 1 2 
    0 6.098919959130616E-5 0.9997275322964165 2.1147850399214744E-4 
    1 7.404648706054873E-37 0.9086408633885092 0.09135913661149081 
    2 0.2284374545687356 7.01786289571088E-11 0.7715625453610858 
    Emission matrix: 
      0 1 2 3 
    0 0.9999997858591223 2.0536163836449762E-39 2.1414087769942127E-7 1.052441093535389E-27 
    1 7.495656581383351E-34 0.2241269055449904 0.4510889999455847 0.32478409450942497 
    2 0.815051477991782 0.18494852200821799 8.465660634827592E-33 2.8603899591778015E-36 
    14/03/22 09:52:21 INFO driver.MahoutDriver: Program took 180 ms (Minutes: 0.003)

The model trained with the input set now is in the file 'hmm-model', which we can use to build a predicted sequence.

    $ $MAHOUT_HOME/bin/mahout hmmpredict -m hmm-model -o hmm-predictions -l 10

To see the predictions:

    $ cat hmm-predictions 
    0 1 3 3 2 2 2 2 1 2


<a name="HiddenMarkovModels-Resources"></a>
## Resources

\[1\]
 Lawrence R. Rabiner (February 1989). "A tutorial on Hidden Markov Models
and selected applications in speech recognition". Proceedings of the IEEE
77 (2): 257-286. doi:10.1109/5.18626.