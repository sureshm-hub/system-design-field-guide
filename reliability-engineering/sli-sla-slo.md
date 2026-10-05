# SLA vs SLO vs SLI

These three terms are related, but they mean different things. Knowing the 
difference helps you define what to measure, aim for, and promise your customers.

## Here's how they actually connect:

- SLI (Service Level Indicator): This is the metric you're measuring. For a 
login service, it could be the ratio of successful login requests to total 
valid requests. It tells you how your service is performing right now.

- SLO (Service Level Objective): You take that SLI and define a target 
around it. Something like "login availability should stay above 99.9% over a 
rolling 28-day window." When you're missing your SLO, it’s a signal to find out 
what's failing before customers notice.

- SLA (Service Level Agreement): This is what you promise your customers in 
a contract. It's usually set lower than the SLO, say 99.5% monthly 
availability. If you breach it, you owe service credits.

If your SLO and SLA are both set to 99.9%, then the moment your availability 
drops below 99.9%, you've already breached the agreement.

> The SLI tells you where you stand.

> The SLO tells you where you should be. 

> The SLA tells your customers what they can expect.

- gap between SLO and SLA is where the real program design decision lives, 
and most teams set it too narrow.

- ex: SLO at 99.9%, SLA at 99.5% gives you an error budget to absorb incidents 
before they become contractual breaches. 
Compress that gap and you lose the runway to investigate, remediate, and 
communicate before credits trigger.

- At $225M program scale, the SLO SLA buffer wasn't a reliability metric, it 
was a risk management decision that determined how much operational 
headroom the program had before a customer conversation became a contractual one.

## How to define:

- A good SLO usually starts with customer expectations and business risk, 
then gets refined with real operational data over time.

- Run 2-4 weeks of real traffic, let the SLI show what's actually achievable, 
then set your SLO slightly above that baseline.

- Always keep a gap between SLO and SLA — that gap is your error budget. 
Collapse it, breach the SLA, and it's no longer an abstract reliability 
problem. It's actual money leaving your company in service credits.