---
layout: post
title: 'Why use Planning Poker cards instead of saying your
estimate out loud?'
date: 2016-09-25
author: Ruslan Ledesma-Garza
summary: 'This article helps you understand why you really want to
follow all steps of Planning 
Poker and use the right tools, specially when you run a
distributed Scrum team. This article makes for a good reference that
you can share with any team member.'
---

![Planning Poker deck](/assets/2016-09-25.planning-poker.jpg)
<small>_Planning Poker deck by [Rachmaninoff](https://commons.wikimedia.org/wiki/User:Rachmaninoff), licensed under the [Creative Commons Attribution-Share Alike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/deed.en) license._</small>

Having experienced good and bad Scrum practices, I would like to
help you understand why you really want to follow [all steps of Planning
Poker](#planning-poker) and use [the right tools](#tools), specially when you run a
distributed Scrum team.

When you estimate user stories for a Scrum sprint, you want to
estimate as accurately as possible given the information you have.
Inaccurate estimates are a problem because they may cause
you to drop stories (which is bad for business) or to carry through more
work than you expected (which is bad for you).

When you choose to just say your estimate out loud after discussing a
story instead of following all steps of Planning Poker, your estimates
may be inaccurate for at least two reasons.
The two reasons affect you even more when you run a distributed
Scrum team.
Let me explain the reasons and why I believe you are better off
estimating by Planning Poker.


# Reason 1: Team members may be biased

Team members may be biased away from an accurate estimate because when
you say your estimate out loud, at least
**anchoring bias** or **peer pressure** may happen.
When either happens, you take into account information
that is irrelevant to your estimate and thus produce an inaccurate
estimate.
Anchoring bias and peer pressure are phenomena that are very difficult
to overcome once they happen.

Anchoring bias happens when you learn the estimate of another team
member before you select your own estimate.
Anchoring bias consists in that you select an estimate that is similar
to the estimate that you just learned.
Anchoring bias is very difficult to overcome because it
is unconscious and neither sheer will nor expertise are effective
against it.
Consider the following three experiments.

**Experiment 1. Anchoring happens unconsciously.**
[Wilson, Houston, Etling, and Brekke](#wilson) applied the following experiment
(_Study 1_ in their report) to a number of psychology
students.
They found that anchoring happens unconsciously when you have in mind
a number even if that number is irrelevant to the estimation that you
are about to do and nobody asks you to compare that number to whatever
you are estimating.
In the experiment, each participant received a number.
Each participant was told that the number was random when in reality
everyone received the same number.
Some participants were asked to indicate if they believed that the
number was lower, greater or equal to the number of countries in the
United Nations.
The rest were asked to compare the number with the number of physicians in
their local phone book.
Finally, all participants were asked to estimate the number of
countries in the United Nations.
The analysis of experimental results indicates that estimates are
biased towards the given number regardless of whether participants
compared that number to the question that they estimated or not.

**Experiment 2. Anchoring happens even if you try compensate.**
[Wilson, Houston, Etling, and Brekke](#wilson) applied the following
experiment (_Study 5_ in their report) also to a number of psychology
students.
They found that anchoring is very difficult to counter even when you
are warned that anchoring will happen.
The experiment compared two conditions.
One condition consists in giving the same anchor to each participant,
asking them to indicate if the number was lower, greater or equal to
the number of physicians in the local phone book, and asking them to
estimate the precise number of physicians. 
The other condition is the same as the first except that participants
where explained that anchoring bias would occur and that they should
avoid it.
The analysis of the experimental results indicates that there is no
significant difference between the biases given by the conditions.

**Experiment 3. Anchoring bias happens even if you are an expert.**
[Englich, Mussweiler, and Strack](#englich) did the following
experiment (_Study 1_).
They found that an anchor that is irrelevant from the legal point of
view influenced the sentencing decisions of legal professionals.
Participants were legal professionals that were either judges or had
been judges at some point.
Participants were asked to consider realistic case material about an
alleged rape from the point of view of a criminal judge.
Afterwards, participants were exposed to a potential sentence via a
questionnaire.
The questionnaire asked that participants imagine a situation where
a journalist interviews them and introduces the potential sentence.
Experimental results indicate that participants were biased by the
potential sentence suggested by the journalist.

Peer pressure consists in that the majority of estimates that other
members announce are the same.
Peer pressure is difficult to overcome because even when you have
reason to stick to a particular estimate, you may still choose the
estimate that the majority announced.
Consider the following experiment.

**Experiment 4. Peer pressure can be more powerful than facts.**
The experiment is by [Solomon Asch](#asch) (_Experiment I_ in
his report).
He found that for a clear matter of fact, people under peer pressure
give inaccurate estimates way more often than when they are not under
peer pressure.
He also found that under peer pressure, the majority of people will
eventually give you an inaccurate estimate.
The experiment is the following.
The experiment happened in a classroom.
Each run of the experiment consisted of 18 trials.
Each trial consisted in matching one line drawn on a card (the
_standard_) with one of three other lines on another card.
In each trial, the corresponding pair of cards was placed on the ledge
of the blackboard.
For each run of the experiment, 8 people participated.
One of them was the subject and the rest were collaborators with
Solomon.
In each trial, each participant was asked to announce publicly their
estimate.
The subject was always second to last in announce his estimate.
For each run of the experiment, the estimates given by the
collaborators followed the same pattern.
Collaborators estimated correctly in the first two trials and then
estimated 12 of the remaining trials unanimously and incorrectly.
Those 12 trials are the _critical trials_.
In between the critical trials, collaborators estimated correctly the
remaining 4 trials.
The control experiment was the same except that participants did not
announce their estimates publicly.
The experimental results are the following.
Without peer pressure, subjects estimated correctly 99% of the critical
trials and thus Asch concludes that the differences between the
standard line and the rest were clearly distinguishable.
Under peer pressure, estimates were correct 63% of the time and for
this reason Asch concludes that peer pressure has a significant
effect on estimates.
Without peer pressure, 5% of subjects gave some incorrect estimate.
Under peer pressure, 76% of subjects gave some incorrect estimate.
Thus, a given person that is under pressure will probably give you a
bad estimate at some point.

You are better off estimating with Planning Poker
because Planning Poker rules out anchoring bias and peer pressure.
The reason is that Planning Poker requires that each team member
**privately** selects their estimate ([step 3](#planning-poker)) and that all
estimates are revealed **simultaneously** ([step 4](#planning-poker)).
This way team members do not know the estimate of any of the other
members when estimating.

If you are not sure that there is a point in hiding estimates
after the first estimation round, consider the way I see the
situation.
Once the team discusses the estimates they chose for a story
(in [step 6](#planning-poker)), that discussion and the estimates they chose
become **relevant** information for the second round (when they
go back to [step 3](#planning-poker)).
The same is not true for the estimates that will be produced in the
second round.
The estimates produced in the second round are irrelevant because they
correspond to somebody else's appreciation of the story.
Thus, Planning Poker rules out anchoring bias and peer
pressure in all estimation rounds.


# Reason 2: Team members may not really understand the story

When team members say their estimates out loud, you can pick one of the
previous estimates to try to get away with not justifying your choice.
Trying to get away with not justifying your choice is unacceptable
even if the reason is that you are trying to avoid being embarrassed
because you do not really understand the story.

In my experience, even people that would understand stories during
Planning Poker do miss the opportunity to understand stories when
Planning Poker is not applied.
I believe that there are two ways of explaining why these team members
understand better by Planning Poker.

**Explanation 1.** One explanation is that without estimates to copy,
the next best thing team members can do to avoid embarrassment is to
actually understand the story.

**Explanation 2.** The other explanation is that Planning Poker makes
you responsible for your estimate.
The reason is that Planning Poker substitutes the impression that
estimating is a group activity with the impression that estimating is
an individual activity.
You are motivated because taking responsibility for your work is
rewarding to you.

I like Explanation 2 better because I feel that, out of the two
explanations, it is the most constructive reason why you are better
off estimating by Planning Poker.
I agree with [Rubin](#rubin) when he says that the majority of the
value of Planning Poker is the discussion and better understanding of
user stories that Planning Poker fosters.


# <a name="tools"></a>Team members should use cards or a special app

When you estimate, you should be really using Planning Poker cards.
If you do not have cards, try using a mobile app.
If you do not have smartphones, give everyone pen and paper and tell them
to write their estimates there.

When you are running a distributed team, do not just write your
estimates in a chat one after the other.
**Ask people to use a dedicated tool, like
my own [spoker-master](https://spoker-master.com/).**

Really, there is no excuse for saying your estimates out loud.


{% include share.html %}


# <a name="planning-poker"></a>Appendix: The Planning Poker Method

[Kenneth Rubin](#rubin) explains in his book _Essential Scrum: A
practical guide to the most popular agile process_ that Planning Poker consists of the following
steps.

1. The product owner selects a user story to be estimated and reads
   the story to the team. 
2. Development team members discuss the story and ask clarifying
   questions to the product owner, who answers the questions.
3. Each estimator **privately** selects a card representing his estimate.
4. Once each estimator has made a private selection, all private
   estimates are **simultaneously** exposed to all estimators.
5. If everyone select the same card, we have consensus, and that
   consensus number becomes the estimate of the story.
6. If the estimates are not the same, the team members engage in a
   focused discussion to expose assumptions and
   misunderstandings. Typically we start by asking the high and low
   estimators to explain or justify their estimates.
7. After discussion, we return to step 3 and repeat until consensus is
   reached.

# References

<dl>
  <dt id="asch">
    Asch
  </dt>
  <dd>
    Asch, S. E. (1956). <a href="http://innovationfootprints.com/wp-content/uploads/2015/06/minority-v.-majority-asch1956.pdf">Studies of independence and conformity: I. A
    minority of one against a unanimous majority.</a> Psychological
    Monographs: General and Applied, 70(9), 1-12. doi:10.1037/h0093718
  </dd>
  <dt id="englich">
    Englich et al.
  </dt>
  <dd>
    Englich, B., Mussweiler, T., & Strack, F. (2006). <a href="http://www.eucim-te.eu/data/dppsenglich/File/PDFSStudien/PSPB_32(1).pdf">Playing Dice With Criminal Sentences: The Influence of Irrelevant Anchors on Experts' Judicial Decision Making.</a> Personality and Social Psychology Bulletin, 32(2), 188-200. doi:10.1177/0146167205282152
  </dd>
  <dt id="rubin">
    Rubin
  </dt>
  <dd>
    Rubin, K. S. (2012). Essential Scrum: A practical guide to the most popular agile process (pp. 129-133). Upper bSaddle River, NJ: Addison-Wesley.
  </dd>
  <dt id="wilson">
    Wilson et al.
  </dt>
  <dd>
    Wilson, T. D., Houston, C. E., Etling, K. M., & Brekke, N. (1996). <a href="http://www.communicationcache.com/uploads/1/0/8/8/10887248/a_new_look_at_anchoring_effects-_basic_anchoring_and_its_antecedents.pdf">A new look at anchoring effects: Basic anchoring and its antecedents</a>. Journal of Experimental Psychology: General, 125(4), 387-402. doi:10.1037/0096-3445.125.4.387
  </dd>
</dl>


{% include comments.html %}
