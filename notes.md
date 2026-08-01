# Day 1 — Returns, log returns, and what the data actually is

## 1. What is a return?

A return is the gain or loss you make, as a proportion of what you started with, over a period of time.

$$r_t = \frac{P_t - P_{t-1}}{P_{t-1}}$$

$1 + r_t$ is the **growth factor** (or gross return) for that period.

## 2. Why don't simple returns add up across time?

Example: 100 → 110 → 100.

- $r_1 = +0.1$
- $r_2 = (100 - 110)/110 = -0.0909\ldots$
- True overall return over the two periods: $0$

**Adding the returns fails:** $r_1 + r_2 = 0.1 - 0.0909 = +0.0091 \neq 0$.

**Multiplying the growth factors works:** $(1 + r_1)(1 + r_2) = 1.1 \times 0.9091 = 1$, and $100 \times 1 = 100$. Correct.

So simple returns compound **multiplicatively**, not additively.

## 3. What is a log return?

$$\ell_t = \ln\left(\frac{P_t}{P_{t-1}}\right) = \ln(P_t) - \ln(P_{t-1})$$

"Log" here means the **natural logarithm**, base $e$. Also called the **continuously compounded return**.

The algebra that turns the product into a sum uses two log laws:

- $\ln(ab) = \ln(a) + \ln(b)$
- $\ln(a/b) = \ln(a) - \ln(b)$

So:

$$\ln\left(\prod_{i=1}^{n}(1 + r_i)\right) = \sum_{i=1}^{n} \ln(1 + r_i) = \sum_{i=1}^{n} \ell_i = \ln\left(\frac{P_n}{P_0}\right)$$

The middle sum telescopes — every intermediate $\ln(P_i)$ cancels.

## 4. The property that makes log returns worth using

**Log returns are additive across time.** Simple returns are not.

Concretely: to get a weekly return from five daily log returns, you just add the five numbers. With simple returns you would have to multiply five growth factors and subtract 1.

(Note: log returns are *not* cheaper to compute per day — `ln` is more work than a subtraction and a division. Additivity is the reason, not speed.)

## 5. What is the S&P 500?

An **index** — a single number computed from a basket of shares — built from 500 large US companies that meet published eligibility criteria. It is not a fund and cannot be bought directly.

Constituents are weighted by **market capitalisation**: the more a company is worth, the more it moves the index. A 10% fall in a £100m company moves the index more than a 10% fall in a £10m company.

The level is total constituent market cap divided by a **divisor**, chosen so the index equalled a set value on a base date. So the level is measured in **index points** and is arbitrary in absolute terms — only ratios of levels within the same series carry meaning.

## 6. Why aren't FTSE 8,000 and S&P 6,000 comparable?

Because each index's level depends on its own arbitrary divisor and base date. Change the divisor and every level in the history changes while nothing real changes.

What *is* comparable: **ratios** — i.e. returns — within a series, and returns between series.

## 7. Two events that make raw closing prices produce fake returns

**Dividends.** The company pays out $x$ per share in cash, and the share price falls by roughly $x$. The naive formula records a loss, but the shareholder is net neutral — they hold the cash.

**Splits and reverse splits.**

- 10-for-1 split: $100 → $10. Naive formula says the share fell 90%. The shareholder experiences no change, because 1 share became 10.
- 1-for-10 reverse split: $2 → $20. Naive formula says the share rose 10×. The shareholder experiences no change, because 10 shares became 1 — fewer shares, same total value.

## 8. What is back-adjustment?

All prices **before** the event are multiplied by a factor — less than 1 for a dividend or a forward split, greater than 1 for a reverse split — so that the whole series sits on one consistent scale. The result is the **adjusted close**, and it accounts for both dividends and splits.

Earlier prices are adjusted rather than later ones so that the most recent price still matches what the market actually quotes today.

## 9. Why does `auto_adjust` change AAPL but not `^GSPC`?

**AAPL** is a stock: it has split (2005, 2014, 2020) and it pays dividends, so both adjustments apply.

**`^GSPC`** is a **price index**:

- splits are absorbed by the **divisor**, so the index level never jumps in the first place
- dividends are **never included** in a price index at all

So there is nothing left for `auto_adjust` to do, and `Close` and `Adj Close` are identical.

## 10. Why ~252 rows per year rather than 365?

Markets close at weekends and on public holidays.

$52 \times 5 = 260$ weekdays, minus ~9 US market holidays $\approx 251$.

**252 trading days per year** is the standard convention in finance.

## 11. What I predicted wrongly, and why

I claimed the adjusted close is *always* lower than the raw close. False: a reverse split back-adjusts earlier prices **upwards**, by a factor greater than 1.

The reasoning error was asserting "always" without trying to break it myself.