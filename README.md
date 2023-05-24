# Quizbowl Survival Analysis

For this project, I used detailed statistics and Bayesian survival analysis to create simulations of quizbowl matches. Quizbowl has virtually no advanced stats at the moment, even though the stats we do have don't predict outcomes that well. Several teams regularly over or underperform their PPG. I created this project as a way to check whether those outcomes were flukes or based on something our existing stats weren't capturing.

For background: quizbowl is like a harder, more academic version of Jeopardy! played by college teams. The clues in a tossup start out hard and get easier as you go on, so that teams with more knowledge will be able to buzz earlier. Some events, like the national championship held by the Academic Competition Federation (ACF), keep track of where players buzz on each tossup. My project analyzes these stats to produce estimates of team and player strength.

# Regression

To create these estimates, I used a Weibull regression with censoring on the buzzpoint data. Only one team can buzz correctly on each tossup, so the stats on a player or team's buzzes will not include the questions they missed. To account for this problem, I right-censored the buzzpoint data, including questions a player missed, and ran a regression to estimate where the player or team would have answered the question with no opponent. 

My regression included an intercept term along with dummy variables representing high-level categories. I considered estimating strength on each category separately rather than creating one overall equation for each team. However, I decided on one overall equation because team strength across categories is highly correlated. 

I ran the regression using the PyMC3 package, and then sampled buzzpoints from each team to prepare to simulate games. For each team, I sampled and stored ten thousand buzzes from each top-level category. I also created a list representing how many questions from each top-level category would appear in a quizbowl game.

# Simulation

Before creating the simulation, I had to decide how to approximate how often questions would be answered incorrectly. On each tossup, if a team buzzes with the incorrect answer, they incur a "neg" and lose five points. Often, team's neg rates may vary across categories. Different players will have different specialties, and will buzz in at different thresholds of confidence. Trying to estimate a team's neg rate on each high-level category, though, would require working with sample sizes of less than ten tossups. I thus decided to use each team's overall neg rate as the probability that they neg any given question.

I made another approximation to estimate how often tossups would go unanswered. Initially, I planned to interpret tossups as "going dead" if both teams buzzed after 100% of the tossup was written. Under this interpretation, however, simulated teams were failing to answer an unreasonably high percentage of tossups. My first attempt at solving this problem, truncating the likelihood distribution at 100%, failed when the model ran into problems sampling. After exploring the data, therefore, I decided to solve the issue by setting the cutoff for "going dead" at 110%.

To run the simulation, I made a list of twenty items representing the top-level categories of the questions in a round of ACF quizbowl. For each tossup category, I randomly chose two buzzpoints from the sampling distributions of the two teams playing.  Whichever team had the earlier buzzpoint would either get the tossup correct or neg, with probabilities determined by their overall neg rate. If they got the tossup correct, they would get 10 points and a bonus, points from which would be awarded based on their ppb. If they got the tossup incorrect, they would lose 5 points, and the other team would get a chance to answer; if the other team's buzzpoint was less than 110, that team would then get the tossup and bonus.

# Results

I took the six highest-finishing teams from ACF Nationals 2023 and simulated 1000 games between each of them. I then plotted the results, which can be found in the "histograms" folder. The results confirmed that some teams are systematically better than ppb and ppg indicate. Cornell A in particular, which had high finishes at both national tournaments in 2023, appears much stronger based on buzzpoint stats than on ppg. Georgia Tech A similarly outperformed their ppg stat in the simulations. As all other results pass the usual sanity checks, this modeling approach seems useful for predicting team strength at future tournaments.
