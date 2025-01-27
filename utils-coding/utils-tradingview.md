# TRADINGVIEW CHEATSHEET

## Generic onliner

```javascript
getPos() => strategy.position_size
isIn() => strategy.position_size != 0
isFlat() => strategy.position_size == 0
isLong() => strategy.position_size > 0
isShort() => strategy.position_size < 0
isNotLong() => strategy.position_size <= 0
isNotShort() => strategy.position_size >= 0
```

## Generic multiline

```javascript
formatBigNumber(num) =>
    var string formattedNum=na
    if num >= 1000000000
        formattedNum := str.tostring(math.round(num/1000000000, 2)) + "B"
    else if num >= 1000000
        formattedNum := str.tostring(math.round(num/1000000, 2)) + "M"
    else if num >= 1000
        formattedNum := str.tostring(math.round(num/1000, 2)) + "K"
    else
        formattedNum := str.tostring(num)
    formattedNum

calcPosSize(riskPerc, usePosSize) =>
    riskEquity  = (riskPerc / 100) * strategy.equity
    atrCurrency = (ta.atr(20) * syminfo.pointvalue)
    posSizeInit = usePosSize ? math.floor(riskEquity / atrCurrency) : 1
    posSize = posSizeInit > 1000000000 ? 1000000000 : posSizeInit < 0 ? 1 : posSizeInit
    posSize
```


## Exit after x bars in trade

```javascript
exitLongAfterBars = input.int(title="Exit Long After N bars", defval=15, minval=1) 
exitShortAfterBars = input.int(title="Exit Short After N bars", defval=15, minval=1)

condLong = false // add here your logic
condShort = false // add here your logic
condEnterLong = isNotLong() and condLong
condEnterShort = isNotShort() and condShort
condExitLong = isLong() and ta.barssince(condEnterLong) >= exitLongAfterBars -1 
condExitShort = isShort() and ta.barssince(condEnterShort) >= exitShortAfterBars -1
```


## Exit after x percent gains in trade

```javascript
exitLongAfterPercents = input.float(title="Close Long After X Percents", defval=3, minval=1)
exitShortAfterPercents = input.float(title="Close Short After X Percents", defval=3, minval=1)

lastEntryPrice = strategy.opentrades.entry_price(strategy.opentrades - 1)
currentPosPercent = isLong() ? ((close - lastEntryPrice) / lastEntryPrice) * 100 : isShort() ? ((lastEntryPrice - close) / close) * 100 : 0
condExitLong = isLong() and currentPosPercent >= exitLongAfterPercents
condExitShort = isShort() and currentPosPercent >= exitShortAfterPercents

// exemple to get the DCA price: https://www.tradingcode.net/tradingview/open-trade-entry-price/
// exemple to get the entry ID (L1 / S1) can be used to get the info of a specific trade
// https://www.tradingcode.net/tradingview/open-order-identifier/
```

