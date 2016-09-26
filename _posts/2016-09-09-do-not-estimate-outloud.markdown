---
layout: post
title: 'Why use Planning Poker cards instead of saying your
estimate outloud?'
date: 2016-09-25
author: Ruslan Ledesma-Garza
summary: 'This article helps you understand why you really want to
follow all steps of Planning 
Poker and use the right tools, specially when you run a
distributed Scrum team. This article makes for a good reference that
you can share with any team member.'
---

<!--
# Outline

- Q. Why would you use planning poker when you can just say your estimate outloud?
- A. I believe planning poker produces more accurate estimates.
- S. If you undestand what planning poker does for you, you might
  resist the urge to estimate with bias.
- Planning poker rules out anchoring bias.
  - Estimates given outloud by other people influence your
    estimate. This is called **anchoring**.
    - It does not matter whether you discuss a given user story and
      then you hear another estimate or the other way around,
      anchoring will happen.
    - Evidence
      - Anchoring happens when you estimate. Real state experiment by
        [Northcraft and Neale](#northcraft).
      - Anchoring is unconcious. Study 1 by [Wilson, Houston, Etling,
        Brekke](#wilson).
      - Anchoring is difficult to avoid. Study 5 by [Wilson et al.](#wilson)
      - Anchoring is difficult to avoid even for experts. Study 1 by
        [Englich and Mussweiler](#englich).
  - When you do planning poker, you ignore others estimates.
    - Reason is that all estimates are revealed simultaneously. [Rubin](#rubin)
- Planning poker motivates you to understand the story.
  - When people understand the story, people can assign an accurate
    estimate.
  - When you skip step 4 of planning poker, you are not motivated to
    understand even if you discuss the story, because there is no
    consequence of not understanding. My experience.
  - **TODO** When you apply
    planning poker, you gain critical understanding of the story
    during the discussion.
    According to [Rubin](#rubin) and my experience.
  - **TODO** I believe that the reason is that you will have to
    estimate by yourself and justify your estimate.

# Post
-->

Having experienced good and bad Scrum practices, I would like to
help you understand why you really want to follow [all steps of Planning
Poker](#planning-poker) and use the right tools, specially when you run a
distributed Scrum team.

When you estimate user stories for a Scrum sprint, you want to
estimate as accurately as possible given the information you have.
Inaccurate estimates are a problem because they may cause
you to drop stories (which is bad for business) or to carry through more
work than you expected (which is bad for you).

When you choose to just say your estimate outloud after discussing a
story instead of following all steps of Planning Poker, your estimates
may be inaccurate for at least two reasons.
The two reasons affect you even more when you run a distributed
Scrum team.
Let me explain the reasons and why I believe you are better off
estimating by Planning Poker.


# Reason 1: Team members may be biased

Team members may be biased away from an accurate estimate because when
you say your estimate outloud, at least
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
<!--
There are several explanations of the mental process that produces
anchoring bias, for example **TODO: footnote**.
However, I consider more practical for you that you understand that
anchoring is unconscious and is very difficult to overcome
by sheer will or expertise.
-->
Anchoring bias is very difficult to overcome because it
is unconscious and neither sheer will nor expertise are effective
against it.
Consider the following three experiments.

_Experiment 1. Anchoring happens unconsciously._
[Wilson, Houston, and Etling](#wilson) applied the following experiment
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

<!--
Study 2.
Conclusion: the more you attention you pay to the number, the more
biased you become.
Same conditions as in study 1 except the following.
The number given is supposed to be an ID.
The number given is between 1928 and 1935.
Instead of two conditions, there are five.
1. red-blue: check if color of number is red or blue. Every number was blue.
2. four digit: check if number is 4 digit number.
3. greater than 100: check if number is greater than 100.
4. greater than 1920 or 1940: given one choice, check if number is
   greater than choice.
5. phone book: check if number is less than, greater or equal to the
   number of physicians in the yellow pages.
The dependent measure was the number of physicians in the phone book.
-->

<!--
Study 4.
Conclusion: Offering monetary incentives had no influence on the
magnitude of anchoring effects.
Same conditions as study 2 except the following.
Participants were told that an award would be given to the person
whose answer to one of the questions was most accurate.
Instead of five conditions there are 2 crosscut conditions.
1. Incentive manipulation
  1. incentive condition: participants were told that the prize question
     was the dependent variable (the number of physicians).
  2. no-incentive condition: participants were told that the prize
     question was another question that everyone had (number of varieties
     of rice).
2. Anchor
   1. Half the people were given an anchor and asked to compare it to
   the question.
   2. Half the people were not given an anchor.
-->

<!--
_Example 2. Anchoring bias cannot be countered by sheer will._
[Wilson, Houston, and Etling](#wilson) ran another experiment (_Study
5_ in their report).
They found that anchoring is very difficult to counter even when you
are warned about anchoring.
Instead of the crosscut conditions of study 4, they had the
following 8 conditions.
1. control: no anchor
2. comparison: anchor and compare against target question (number of
   physicians in phone book).
3. contamination: written warning "a number in people's heads can
   influence their answers to subsequent questions", hypothetical and
   unrelated example, and asked people to compensate for anchoring
   before giving the anchor.
4. underestimation: same as contamination except that we told people
   (wrongly) that large numbers cause them to decrease their
   estimates.
5. overestimation-general: same as underestimation but warning is that
   large numbers cause people to overestimate their estimates
   (correct).
6. overestimation-specific: same as previous except that participants
   were told that the ID they would be given might influence their
   estimate.
7. after-ID: same conditions but warning given after receiving
   their ID but before comparing it to the question.
8. after-comparison: same conditions but warning given after
   comparing it to the question.
9. after-estimation: same conditions but warning given after
   estimation, they were given the chance to change their estimate.
Their analysis of experimental results indicates that the anchoring
condition differed significantly from the control condition and that
none of the anchoring conditions differed significantly from each
other.
-->

_Experiment 2. Anchoring happens even if you try compensate._
[Wilson, Houston, and Etling](#wilson) applied the following
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

<!--
Study 1.
[Englich, Mussweiler, and Strack](#englich) ran an experiment that
show that anchoring bias will happen even if you are an expert in
whatever you are estimating.

Hypothesis: Clearly irrelevant anchor influences the sentencing
decisions of legal professionals.

Participants were legal professionals that were either judges or had
been judges at some point.
Participants were asked to consider realistic case material about an
alleged rape from the point of view of a criminal judge.
Afterwards, participants were exposed to a potential sentence via a
questionaire.
The questionnaire asked that participants imagine a situation where
a journalist interviews them and introduces the potential sentence.

Experimental results indicate that participants were biased by the
potential sentence suggested by the journalist.

-->

_Experiment 3. Anchoring bias happens even if you are an expert._
[Englich, Mussweiler, and Strack](#englich) did the following
experiment (_Study 1_).
They found that an anchor that is irrelevant from the legal point of
view influenced the sentencing decisions of legal professionals.
Participants were legal professionals that were either judges or had
been judges at some point.
Participants were asked to consider realistic case material about an
alleged rape from the point of view of a criminal judge.
Afterwards, participants were exposed to a potential sentence via a
questionaire.
The questionnaire asked that participants imagine a situation where
a journalist interviews them and introduces the potential sentence.
Experimental results indicate that participants were biased by the
potential sentence suggested by the journalist.


<!--
Social Pressure, Peer Pressure, Conformity
Experiments by Solomon Asch, also known as:
  - Asch conformity experiments
  - Asch Paradigm

Article:
Opinions and Social Pressure
http://www.columbia.edu/cu/psychology/terrace/w1001/readings/asch.pdf

Experiment conditions:
- Group of seven to nine youg men in a classroom.
- All except one participant are confederates of the experimenter.
  The remaining participant is the subject.
- 123 subjects were applied the experiment
- Group is told that experiment is on visual judgement.
- Each run of the experiment consists of 18 rounds.
- Group is told to compare the lenghts of lines.
- In each round there are two cards.
- One shows a single vertical black line, the standard.
- The other shows three lines of various lengths, one is the same
  length as the standard.
- Participants are asked to choose from the second card the line that that
  is of the same length as the line on the first card.
- In the first two rounds, everyone chooses the matching line.
- In subsequent rounds, all confederates choose a line that does not
  match.
- The confederates choose the right answer from time to time to reduce
  suspicion.
- When subject becomes suspicious, the run of the experiment is
  discarded.
- Out of the 18 rounds, in 12 rounds the confederates choose a wrong
  line.
- Without peer pressure, subjects choose a wrong line less than 1
  percent of the time.
- With peer pressure, subject choose a wrong line 36.8 percent of the
  time.

Conclusion: peer pressure has significant effect on subjects.
-->

<!--
Article:
Studies of independence and conformity: a minority of one against a
unanimous majority
http://innovationfootprints.com/wp-content/uploads/2015/06/minority-v.-majority-asch1956.pdf

Summary:
- Disagreement betwen a single person and a group.
- Disagreement on a simple and clear matter of **fact**.

Conditions:
- Each group consists of 7 to 9 participants.
- All participants were male college students.
- All but one of the group were instructed to respond on certain
  trials with wrong and unanimous judgements.
- The other participant was unaware of the arrangement
  --the subject--. 
- "A minority of one against a wrong and unanimous majority."
- The subject was nearly always seated before the last member of the majority.
- Group was asked to match the length of a given line --the standard--
  with one of three other lines.
- One of the three comparison lines was equal to the standard, the
  other two lengths differed from the standard.
- The participants were asked to announce their judgements publicly in
  the order in which they were seated.
- A run of the experiment consisted of 18 comparisons.
- The experiment was run for 123 subjects.
- The subject heard the majority respond unanimously in 12 comparisons
  with estimates that clearly contradicted his own observation.
- The rest of the comparisons were answered correctly by the
  confederates.
- Under control conditions, accuracy  was of over 99 percent.

Quantitative results:
- An estimate given by the subject in a **critical trial** is either
  - Correct and thus independent of the majority
  - incorrect and either identical with that of the majority or in its
    direction.
- Number of incorrect estimates is index of effect exerted by majority
  upon the subject.
- The number of incorrect estimates may vary from 0 to 12 for each run
  of the experiment.
- How often subjects erred in critical trials? Table 3 shows frequency
  distribution of errors on critical trials.
  - Errorless performances
    - Control group had 95% of errorless performances.
    - Experimental groups had 25% of errorless performances.
  - Errors in critical trials
    - Control group had less than 1% of errors in critical trials.
    - Experimental groups had 37% of errors in critical trials.
  - The differences between the control and experimental groups are
    highly significant.

Conclusion:
1. Each unanimous wrong majority produced a marked and significant
   distortion in the reported estimates.
2. In critical trials, the majority of estimates was correct.
3. Under no peer pressure, estimates are more accurate and vary less.
4. Some subjects may resist very well peer pressure, while others may
   follow the majority all the time.
   - 24% of subjects gave errorless estimates
   - 28% of subjects gave majority-determined estimates, from 8 to 12
     times.
   - The rest of the subjects (48%) had errors from 1 to 7 times.
-->

<!--
Recreation of the Asch conformity experiment with robots
Brandstetter et al. 2014.

Conclusion: Robots do not create peer pressure.
-->

Peer pressure consists in that the majority of estimates that other
members announce are the same.
Peer pressure is difficult to overcome because even when you have
reason to stick to a particular estimate, you may still choose the
estimate that the majority announced.
Consider the following experiment.

_Experiment 4. Peer pressure can be more powerful than facts._
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
Salomon.
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

<!--
Does Planning Poker rule out anchoring bias only in the first round?
Does planning

Is it a problem that people know everyone's estimates after the first
round?

Is confluence towards estimate of experienced members anchoring bias?
-->

If you are not sure that there is a point in hidding estimates
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

When team members say their estimates outloud, you can pick one of the
previous estimates to try to get away with not justifying your choice.
Trying to get away with not justifying your choice is unacceptable
even if the reason is that you are trying to avoid being embarrassed
because you do not really understand the story.

In my experience this happens to team members that understand
stories when applying Planning Poker.
I believe that there are two ways of explaining why these team members
understand better by Planning Poker.

_Explanation 1._ One explanation is that without estimates to copy,
the next best thing team members can do to avoid embarrassment is to
actually understand the story.

_Explanation 2._ The other explanation is that Planning Poker makes
you responsible for your estimate.
The reason is that Planning Poker substitutes the impression that
estimating is a group activity with the impression that estimating is
an individual activity.
You are motivated because taking responsibility for your work is
rewarding for you.

I like the Explanation 2 better and I think that that is the way you
should be looking at why you are better off estimating by Planning
Poker.
I agree with [Rubin](#rubin) when he says that the majority of the
value of Planning Poker is the discussion and better understanding of
user stories that Planning Poker fosters.


# Team members should use cards or a special app

When you estimate, you should be really using Planning Poker cards.
If you do not have cards, try using a mobile app.
If you do not have phones, give everyone pen and paper and tell them
to write their estimates there.

When you are running a distributed team, do not just write your
estimates in a chat one after the other.
Ask people to use a dedicated tool, like
[PlanITPoker](http://www.planitpoker.com/) or my own
[spoker-master](https://spoker-master.herokuapp.com/).

Really, there is no excuse for saying your estimates outloud.


{% include subscribe.html %}
{% include share.html %}


# <a name="planning-poker"></a>Appendix: The Planning Poker Method

[Kenny Rubin](#rubin) explains in his book _Essential Scrum: A
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
  <dt id="englich">
    Englich et al.
  </dt>
  <dd>
    Englich, B., Mussweiler, T., & Strack, F. (2006). <a href="http://www.eucim-te.eu/data/dppsenglich/File/PDFSStudien/PSPB_32(1).pdf">Playing Dice With Criminal Sentences: The Influence of Irrelevant Anchors on Experts' Judicial Decision Making.</a> Personality and Social Psychology Bulletin, 32(2), 188-200. doi:10.1177/0146167205282152
  </dd>
<!--
  <dt id="northcraft">
    Northcraft and Neale
  </dt>
  <dd>
    Northcraft, G. B., & Neale, M. A. (1987). <a href="http://web.missouri.edu/segerti/capstone/northcraft_neale.pdf">Experts, amateurs, and real estate: An anchoring-and-adjustment perspective on property pricing decisions.</a> Organizational Behavior and Human Decision Processes, 39(1), 84-97. doi:10.1016/0749-5978(87)90046-x
  </dd>
-->
  <dt id="rubin">
    Rubin
  </dt>
  <dd>
    Rubin, K. S. (2012). Essential Scrum: A practical guide to the most popular agile process (pp. 129-133). Upper Saddle River, NJ: Addison-Wesley.
  </dd>
  <dt id="asch">
    Asch
  </dt>
  <dd>
    Asch, S. E. (1956). <a href="http://innovationfootprints.com/wp-content/uploads/2015/06/minority-v.-majority-asch1956.pdf">Studies of independence and conformity: I. A
    minority of one against a unanimous majority.</a> Psychological
    Monographs: General and Applied, 70(9), 1-12. doi:10.1037/h0093718
  </dd>
  <dt id="wilson">
    Wilson et al.
  </dt>
  <dd>
    Wilson, T. D., Houston, C. E., Etling, K. M., & Brekke, N. (1996). <a href="http://www.communicationcache.com/uploads/1/0/8/8/10887248/a_new_look_at_anchoring_effects-_basic_anchoring_and_its_antecedents.pdf">A new look at anchoring effects: Basic anchoring and its antecedents</a>. Journal of Experimental Psychology: General, 125(4), 387-402. doi:10.1037/0096-3445.125.4.387
  </dd>
</dl>


# Comments

<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */
    var disqus_config = function () {
        this.page.url = 'http://ruslanledesma.com/2016/08/11/the-special-offer-problem.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-08-11-the-special-offer-problem'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    (function() {  // DON'T EDIT BELOW THIS LINE
        var d = document, s = d.createElement('script');

        s.src = '//definecode.disqus.com/embed.js';

        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a
        href="https://disqus.com/?ref_noscript"
        rel="nofollow">comments powered by Disqus.</a></noscript>
