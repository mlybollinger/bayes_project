# bayes_project

For this project, I used detailed statistics and Bayesian survival analysis to create simulations of quizbowl matches. Quizbowl has virtually no advanced stats at the moment, even though the stats we do have don't predict outcomes that well. Several teams regularly over or underperform their PPG. I created this project as a way to check whether those outcomes were flukes or based on something our existing stats weren't capturing.

For background: quizbowl is like a harder, more academic version of Jeopardy! played by college teams. The clues in a tossup start out hard and get easier as you go on, so that teams with more knowledge will be able to buzz earlier. Some events, like the national championship held by the Academic Competition Federation (ACF), keep track of where players buzz on each tossup. My project analyzes these stats to produce estimates of team and player strength.

To create these estimates, I used a Weibull regression with censoring on the buzzpoint data. Only one team can buzz correctly on each tossup, so the stats on a player or team's buzzes will not include the questions they missed. To account for this problem, I right-censored the buzzpoint data, including questions a player missed, and ran a regression to estimate where the player or team would have answered the question with no opponent. 

My regression included an intercept term along with dummy variables representing high-level categories. I considered estimating strength on each category separately rather than creating one overall equation for each team. However, I decided on one overall equation because team strength across categories is highly correlated. 

I ran the regression using the PyMC3 package, and then sampled buzzpoints from each team to prepare to simulate games. For each team, I sampled and stored ten thousand buzzes from each top-level category. I also created a list representing how many questions from each top-level category would appear in a quizbowl game.

Before creating the simulation, I had to decide on methods to approximate how often questions would go unanswered, or be answered incorrectly. On each tossup, if a team buzzes with the incorrect answer, they incur a "neg" and lose five points. Often, team's neg rates may vary somewhat between categories. Different players will have different specialties, and will buzz in at different thresholds of confidence. Trying to estimate a team's neg rate on each individual category, though, runs into issues with small sample sizes. I thus made the simplifying assumption that a team's overall neg rate represents their likelihood to neg on any given question.


