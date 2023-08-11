+++
title = "wOBA, xwOBA, Stuff+, xERA -- It's all wrong!"
date = "2023-07-24"
author = "Sam"
description = "A minor flaw in its calculation has major implications"
+++
![gidp_prob](/GIDP_prob.png)

I've been working on sxwOBA, a baseball statistic that aims to model expected hitting outcomes based on the exit velocity, launch angle, spray direction, and batter sprint speed of a given batted-ball event. The metric was inspired by the statcast metric [xwOBA](https://baseballsavant.mlb.com/leaderboard/expected_statistics) which contrastingly leaves out spray angle. 

As baseball savant notes:
> "Expected Outcome stats help to remove defense and ballpark from the equation to express the skill shown at the moment of batted ball contact."

This approach helps us remove some of the noise and factors that are outside of the batter or pitcher's control for a more robust performance estimation. Aditionally, wOBA and xwOBA are context-neutral, so baserunner state and number of outs are controlled for and hitters are credited with the relative run value of the plate appearance based on [linear weights](https://library.fangraphs.com/principles/linear-weights/). The use of linear weights creates consistency by valuing different offensive outcomes—like walks, singles, and home runs—based on their average historical impact on scoring runs. These metrics credit players based on the inherent value of their performance, independent of the specific game situation. So, a home run is valued the same whether it's a solo shot or a grand slam because these stats aim to evaluate the hitter's skill, not their luck of having runners on base.

If you look closely at the wOBA equation, you'll notice that it only credits hitters for positive outcomes, such as walks or hits. All outs are treated the same, they simply add to the denominator, with no change to the numerator. 

> ![Equation](https://math.vercel.app/?color=white&from=w%20O%20B%20A%3D%5Cfrac%7B0.7%20%5Ctimes%20%28BB%2BHBP%29%20%2B%200.9%20%5Ctimes%201B%20%2B%201.25%20%5Ctimes%202B%20%2B%201.6%20%5Ctimes%203B%20%2B%202%20%5Ctimes%20HR%7D%7BAB%2BBB%2BSF%2BHBP-IBB%7D.svg)

But let's take a step back for a second and think about double plays. They are treated the same as a single out because any events where the batter does not get on base are treated the same (+0 to the numerator, +1 to the denominator). This means that wOBA will neglect the value of *all* double plays. This is a major issue because wOBA and its variations are used in some of the most commonly used stats in the baseball community. wRC+, xwOBA, and xERA all inherit this bias from wOBA.

For some players this bias is negligible, for others it is substantial. It really just depends how much of an outlier the player is in terms of their frequency of double plays. A hitter who grounds into many plays will be *less* productive than their wOBA indicates, and a hitter who grounds into few double plays will be *more* productive than their wOBA indicates.

Now, wOBA is mainly an offensive stat, but it has been adapted to evaluate pitchers in several different cases and they also fail to take into account double plays. Baseball savant’s expected ERA (xERA) takes a pitcher’s xwOBA and and puts it on an ERA scale so that we can get a better picture of run prevention skill with respect to contact quality.

Similarly, using wOBA to measure the effectiveness of different pitch types results in the undervaluing of pitches like sinkers and changeups, which more often result in double plays. As a result, [some have even written off sinkers as a dying pitch](https://blogs.fangraphs.com/go-see-the-two-seamer-before-its-gone/) in recent years simply due to the fact that its main strength isn't captured by wOBA.

![pitch_gidp](/pitch_gidp.png)

It gets worse, though. Context-neutral linear weights are not only used in wOBA and its variants, they are also used in stuff quality models in the form of expected run value (xRV). This means metrics such as Stuff+ suffer from the same flaw as wOBA.

So how do we adjust for this? It's not exactly simple. While adding a weight for double plays to the wOBA equation would improve its accuracy in predicting runs scored, it would also break the context neutrality assumption that is foundational to wOBA. 



