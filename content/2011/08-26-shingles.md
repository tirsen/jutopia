---
created_at: 2011-08-26
excerpt: 
kind: article
publish: true
tags: [machinelearning, triposo, python]
title: "Shingles"
excerpt: "What does roof laying and machine learning have in common?"
---


At [Triposo][1] we have a database of restaurants that we've collected from various open content sources. When we display this list in our mobile travel guides one of the key pieces of information for our users is what cuisine the restaurant has. We often have this available but not all the time.

![List of restaurants with cuisines](/assets/images/cuisine-list.png)

Sometimes you can guess the cuisine just by looking at the name:

* "Sea Palace" - probably chinese.
* "Bavarian Biercafe" - probably german.
* "Athena" - probably greek.

A useful technique you will run into very quickly as soon as you start working with machine learning is what is popularly known as *shingles*. (Referring to [roof shingles][3] not the disease.)

![Roof shingles](/assets/images/roof-shingles.jpg "Roof shingles - photo by hasdrupal2000 on Flickr")

Shingles in machine learning is the set of overlapping character n-grams produced from a string of characters. It's common to add a start and end indicator to the strings so that characters at the start and end are treated specially. Hopefully this diagram explains how to produce the shingles from a string.

![4-gram shingles](/assets/images/4gram-shingles.png)

Shingles are easily generated with a one-liner Python list comprehension (n is the size of each n-gram, n=4 often works well):

<pre class="brush: py">[word[i:i + n] for i in range(len(word) - n + 1)]
</pre>

Shingles are great when you want to get a measure of how similar two strings of characters are. The more n-grams that are shared, the more similar the strings are. Since you can use hashing to look up instead of slightly more expensive trees this is an important tool when you're working with really large quantities of data. At Google <code>O(1)</code> rules.

Let's use shingles to guess the cuisine of restaurants given its name.

First of all let's index all the restaurant names where we know the cuisine. We want to know how likely it is that a particular 4-gram has a particular cuisine.

We start with a hash map from 4-gram to observed cuisine probability (in Python [collections.Counter][2] is just great for this). We iterate through all the restaurants and for every 4-gram shingle we bump up that cuisine in the corresponding probability distribution.

Right now we have an association to 4-gram to number of observations but we want to work with probabilities, number of observations is irrelevant. So let's also normalize each histogram. This prevents common restaurants like American and Chinese to score really highly for common n-grams. (This was a problem I initially had.)

So our model looks something like this:

<pre class="brush: py">'bier' => {'german': 0.37, 'currywurst': 0.06, ...}
'{ath' => {'greek': 0.68, 'american': 0.04, 'pizza': 0.04, ...}
...
</pre>

Then to guess the cuisine of a restaurant name we produce the 4-gram shingles and then add together the probability distribution for the cuisines for each 4-gram.

<pre class="brush: py">lookup('bavarian biercafe')
[('german', 2.28), ('italian', 1.17), ('cafe coffee shop', 0.95)]
lookup('athena')
[('greek', 2.64), ('pizza', 0.37), ('italian', 0.36)]
</pre>

The highest scored cuisine is our best guess but the bigger difference it is to the second score the higher confidence we have.

The neat thing with this technique is not only that it grows linearly with the size of the learning set and the size of the inputs. It's also really easy to parallelize. This is why it's so popular when you're working at "web scale".

So, should we use this in our guides? I'm not entirely convinced, for high confidence guesses we are usually correct but a human is also very good at guessing the cuisine. That said, it provided a nice little example of the type of processing we do in our pipeline.

[1]: http://www.triposo.com
[2]: http://docs.python.org/dev/library/collections.html#collections.Counter
[3]: http://en.wikipedia.org/wiki/Roof_shingle