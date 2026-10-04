# Presentation Script 2 — SIMPLE / PLAIN LANGUAGE

**Capstone Project | Data Analytics**
**Title: Predicting India's Crop Production from Past Data**

> A friendly, non-technical version of the same project. Good for a general audience, a short demo,
> or the opening of your talk. Approx. speaking time: 6–9 minutes. Figures are in `report_outputs/`.

---

## 1 — What this project is about

"Hi everyone. My project tries to answer one simple question:

**'Can we use India's past farming records to predict how much of a crop will be grown next year?'**

If we can predict production ahead of time, the government and planners can prepare better — store the
right amount of grain, plan transport, and spot crops whose output is dropping. That's the whole idea."

---

## 2 — The data

"I used a single data file of Indian crop records. Think of it as a giant spreadsheet where each row
says: in this *year*, in this *district*, for this *crop*, in this *season*, this much land was used,
this much was produced, and this was the yield.

It's big and clean: about **450,000 rows**, covering **26 years (1997 to 2022)**, **115 different
crops**, and **every state and most districts** in the country. Almost no missing data."

*(Show: dataset-scale and yearly-observations charts.)*

---

## 3 — Getting to know the data (the fun part)

"Before any prediction, I just looked at the data to understand it.

A few things stood out:
- Crops are hugely different in size. A handful — like sugarcane, rice and coconut — are enormous,
  while many others are small.
- Overall production has generally grown over the years.
- Where you farm more land, you usually produce more — which sounds obvious, but it's nice to confirm
  it in the numbers."

*(Show: national production trend and top-crop trends.)*

---

## 4 — Did we check our hunches? Yes.

"I didn't just eyeball it — I ran proper statistical tests to confirm the patterns are real and not
luck. Three checks:
- Do big-area farms and small-area farms differ in yield? **Yes, clearly.**
- Does a crop's growth trend relate to its yield? **Yes.**
- Do different crops have genuinely different yields? **Absolutely yes.**

All three came back strongly confirmed."

*(Show: hypothesis-test chart.)*

---

## 5 — Teaching the computer to predict

"Then I taught the computer to forecast next year's production. I gave it clues like last year's
production, the year before, how much land was used, and recent trends.

I tested **eight different prediction methods** and compared them fairly — training them on the older
years and then testing on the most recent years they'd never seen.

The best all-round method was a **Random Forest** — basically a 'wisdom of many decision trees'
approach. It captured the ups and downs far better than simple straight-line methods."

*(Show: model comparison chart.)*

---

## 6 — How well did it do? (The honest answer)

"Here's where I'll be straight with you.

When I tested the model year-by-year on the historical data, it was **very accurate — around 95%**.

But on the very last few years, accuracy dropped to about **50%**. Why? Two honest reasons:
- A few giant crops — coconut especially — are so large that one bad guess throws off the whole score.
- And at the very end of the data, some crops had no 'next year' to compare against yet, which confuses
  the model.

So the model is genuinely good for most crops, but struggles on a few extreme, unusual ones. Knowing
*where* it struggles is just as valuable as the prediction itself."

*(Show: actual-vs-predicted chart.)*

---

## 7 — Turning it into something useful

"Finally, I built a simple 'watch list.' The system scores each crop by how risky its forecast is and
how important the crop is, then ranks them.

The top result was **Coconut** — the one crop a planner should double-check before trusting its number.
Everything else was considered reliable. That's the real payoff: not just a prediction, but **advice on
which predictions to trust.**"

*(Show: crop priority chart.)*

---

## 8 — Wrap up

"So, to sum up in one breath:

I took 26 years of India's crop data, confirmed the key patterns with statistics, trained a model that
predicts next year's production well for most crops, honestly measured where it falls short, and turned
it all into a simple watch-list for decision-makers.

If I had more time, I'd add weather and price data and handle those few giant crops better. Thanks for
listening — I'm happy to answer any questions."

---

### One-line summary (if you only have 30 seconds)
"I used 26 years of Indian crop records to predict next-year production. A Random Forest model works
well for most crops, I showed exactly which crops it struggles with, and I built a watch-list that tells
planners which forecasts to trust."
