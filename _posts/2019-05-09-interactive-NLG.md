---
layout: post
title: "Plan, Write, and Revise: Humans Helping Machines to Write (an explanation of the Plan, Write, and Revise paper)"
date: 2019-05-09
---

This is an explanation in (what I attempt to be) plain English of my paper [Plan, Write, and 
Revise: an Interactive System for Open-Domain Story Generation](https://arxiv.org/abs/1904.02357).

This is entirely here to make my work more understandable and accessible, so if anything doesn’t make sense, or you want something explained in more detail, please do let me know!

## Some background
Much of the existing research on interactive storytelling systems is focused on making _humans_ better writers. 
This makes sense intuitively, because for a long time human writers have been using interesting methods to aid 
creativity - methods that add intelligent randomness, beyond what the mind of an individual would 
come up with using just her own home-grown network of neurons. 
These methods range from games like [exquisite corpse](https://en.wikipedia.org/wiki/Exquisite_corpse), 
which shows a human part of an existing story, and forces them to continue the story from just the part<sup>[1](#footnote1)</sup>, 
to true randomness, like picking themes out of a hat or using random word generators.

However, most of the existing research on using neural networks to do this "creativity infusion" is 
not very successful. It’s unclear from results whether a “more-developed randomness-inserter” (which is how a neural-network writing assistant tends to be used) 
is better than a less developed one.  It could end up being a much more expensive and long-winded way to pick a word out of a hat.
The question is: is a full sentence, which is grammatical but kinda weird and wrong in inexplicable ways, more helpful than a series of relevant words to inspire a human writer? 
We don’t know yet. And our neural networks are not quite good enough to pull off exquisite corpse.<sup>[2](#footnote2)</sup>

So, what about having humans help machines? 

This is what my research goes into. I investigate 3 questions:
* Can humans collaborate with neural networks to generate better stories?
* Does the stage at which a human is involved (in the _planning_, in the _writing_, in some of the style controls) matter? Are some types of collaboration more effective than others?
* If a human tries to improve a _specific_ aspect of a story (make it more creative, more relevant, or more coherent) can they do so effectively?

## Set-up:
I start by training a neural network to write 5 sentence stories when given a title. 
Since I want to involve humans in _planning_, not just in _writing_, I train a second network to make a plan from a title (and then train the first network to use that plan as it writes).

A story looks like this:

_Title:_ sick dog

_Storyline (aka, Plan):_ ellie -> unwell -> finally vet -> found -> happy

_Story:_ 
ellie had a dog.
her dog was very lethargic.
she finally took him to the vet.
the vet found out that her dog had had a heart attack.
ellie was happy to be able to take the dog to the vet.<sup>[3](#footnote3)</sup>

I give random humans a little web application<sup>[4](#footnote4)</sup> so that they can co-write 
with my neural network, with _only 5 minutes._ <br>
The interface looks like this (a screenshot has been annotated with sections that a human edited):
<br>
![Screenshot of Interface](https://seraphinatarrant.github.io/images/medium_interactive_annotated_blue.png){:height="50%" width="50%"}
<br>

And then I ask different random humans what they thought of the stories. <br>
Some human creators in the experiment can only control the “creativity level” of the text, 
others can co-write the plot, others can co-write the stories, and others can play with any part 
that they want. Some humans are directed to try to improve certain story aspects.

I also ask the human writers how much they enjoyed the process, and what they think of their own stories, 
so that I can measure the correlation of their experience to the results.

## Wait why do we care again?
Good question. We might care about human objective experience if we’re designing the user experience 
of a piece of software. Even if you’re a researcher like me, it’s good to know what people like to 
do more, because it is easier to get data when you have willing victims to experiment on. 

But the main reason I’m excited about this, is that if it’s successful, it means that humans can, 
with no training and very little time, provide good training data for a neural network. 
And that training data will be extra powerful, because it will be _parallel_.

**What do I mean by this?** <br>
I can try to teach a neural network the difference between a good story and a bad story by giving 
it two short stories from reddit - one with many upvotes, one with almost none. 
But they are on different subjects, and by different authors. So it is hard for the network to determine 
which differences were the source of the difference in quality between the two stories. 
Story A was about Wall Street, Story B about Dragons; Story A was in first-person, Story B was in third, 
Story A had much more dialogue, etc. 
The example is still useful, but it’s very noisy, there’s a lot of information there that doesn’t really help us with the task. 

But if Story A is a story that the neural network tried to write when you asked 
it to write about _The Great Blueberry Disaster_ and Story B is the revised version that the 
human helped with, you suddenly have a much clearer idea of exactly what makes Story B better, 
since most of the differences should be directly related to quality.

That’s the idea. But this depends on humans being able to actually make stories better, rather than just think that they are.

## Extra Set-up
One more thing. <br>
To prove that, if I had this ideal training data, I could use it, I also added _Discriminators_ to my system, for the same qualities that I asked humans to improve. 
If you know about [Generative-Adversarial Networks](https://en.wikipedia.org/wiki/Generative_adversarial_network) (GANs) 
then this is inspired by the second part of that (the adversarial part), though is not technically the same thing. 
If you don’t know about GANs, that’s fine, bear with me. For this context, think of it as a neural network that acts a little bit like an editor.

I had a discriminator for Relevance, and a discriminator for Creativity 
(I also asked humans to work on Coherence, but didn’t have a discriminator for that). 
The Relevance discriminator is another neural network that is trained to distinguish between relevant 
and irrelevant stories, and the Creativity discriminator is the same but trained to distinguish 
between creative and un-creative stories. 

They work like this: every time my main story-writing neural network wants to say something, 
I have it generate multiple hypotheses, for instance, 5 different options for continuing the story. 
And then the Relevance discriminator votes for the options it finds more relevant, and the Creativity 
discriminator votes for the options is finds more creative. The main story-writer has a vote too, 
so the final best option of the 5 is chosen as a combination of their votes. <br>

This is a bit of an oversimplification - since in fact the story-writer has probabilities, and the discriminators adjust those probabilities based on their specific knowledge. But it’s basically voting, just more fine-grained. 

So while the human is trying to make a given story more Creative, the Creativity Discriminator is too.
 It was important to test that the humans were able to give gains in addition to the existing defaults.
 
(Note: I have glossed over how to train the Discriminators before I have human parallel data. It’s not that important, but basically I made my own synthetic parallel data, which is way less good).



## Results
Here are the results from my experiments (I will explain them in English):
<br>
![Results tables](https://seraphinatarrant.github.io/images/NAACL_results_tables.png){:height="75%" width="75%"}
<br>

The takeaways are these:
1. humans _do_ improve the things they are trying to improve (this is exciting!)
1. improving any aspect of the story (focusing on specifically relevance, for instance) tends to improve the story overall
1. in general, humans have more fun when they are allowed to play with more aspects of the writing process, when the interaction is more flexible
1. somewhat surprisingly, despite #3, humans most enjoyed working with only being able to modify story (not more), and other humans also thought those stories were best
1. a human’s judgement of the quality of their own story is not always correlated with independent judgements (shocker)

Result #1 means that we now can have parallel data to train new, better Discriminators! And then every neural network can carry around a pocket human to adjust its writing. 

The other results are good but not that surprising; I was expecting that more interactivity in general would be both more fun and result in better stories, and it does.

But result #4 - why involving humans in only story-writing was in fact the best - requires some thought to work out. No humans were allowed to use the interface multiple times (it would have been too hard to control for familiarity) and 5 minutes is actually very short to learn both a new interface and how to talk to a neural network. So Story-only was best either because less of the limited time was spent learning the system (as there was only one aspect to control) and more spent interacting with the network or because being forced to rely on the network-generated storylines (story plans) made the humans better writers. I believe the former is the stronger factor, but the latter is not implausible either. The positive impact of restrictions on creativity is well known as an effect of the Sonnet form, of Tchaikovsky’s time signatures. To work it out for certain, I’d need a follow up experiment which allowed users to learn the system first, or adjusted time constraints based on complexity of the task.

But first, we train pocket-humans.

### Footnotes
<a name="footnote1">1</a>: in other words, it forces the human mind to model something incorrectly by showing it limited context <br>
<a name="footnote2">2</a>: This is actually somewhat surprising - since the thing that makes _humans_ able to play exquisite corpse is being more like a neural network. But that would be a long discussion, so take my word for it <br>
<a name="footnote3">3</a>: Ta-da! Yes the neural networks wrote that. Also the storyline/plan representation is that way because I designed it to be, not because of anything intrinsic to neural nets. <br>
<a name="footnote4">4</a>: Website: [http://cwc-story.isi.edu/interactive.html](http://cwc-story.isi.edu/interactive.html) <br>