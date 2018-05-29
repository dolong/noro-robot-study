# Noro Robot Study
Pine Script Study with Alerts
Original URL: https://www.tradingview.com/script/vFA4zd3X-robot-bitmex-fast-rsi-v1-0/
```

//@version=2
study(title = "DL Modified Noro Fast RSI", shorttitle = "D RSI", overlay = true)

//Settings
needlong = input(true, defval = true, title = "Long")
needshort = input(true, defval = true, title = "Short")
capital = input(100, defval = 100, minval = 1, maxval = 10000, title = "Lot, %")
pyramiding = input(9, defval = 9, minval = 1, maxval = 100, title = "pyramiding")
rsiperiod = input(7, defval = 7, minval = 2, maxval = 100, title = "RSI period")
rsilimit = input(30, defval = 30, minval = 1, maxval = 50, title = "RSI limit")
rsibars = input(2, defval = 2, minval = 1, maxval = 20, title = "RSI signals")
useocf = input(true, defval = true, title = "Use Open Color Filter")
useccf = input(true, defval = true, title = "Use Close Color Filter")
openbars = input(2, defval = 2, minval = 1, maxval = 20, title = "Open Color, Bars")
closebars = input(1, defval = 1, minval = 1, maxval = 20, title = "Close Color, Bars")
useobf = input(true, defval = true, title = "Use Open Body Filter")
usecbf = input(true, defval = true, title = "Use Close Body Filter")
openbody = input(20, defval = 20, minval = 0, maxval = 1000, title = "Open Body Minimum, %")
closebody = input(20, defval = 20, minval = 0, maxval = 1000, title = "Close Body Minimum, %")
showrsi = input(true, defval = true, title = "Show indicator RSI")
showpyr = input(false, defval = false, title = "Show pyramiding")
fromyear = input(1900, defval = 1900, minval = 1900, maxval = 2100, title = "From Year")
toyear = input(2100, defval = 2100, minval = 1900, maxval = 2100, title = "To Year")
frommonth = input(01, defval = 01, minval = 01, maxval = 12, title = "From Month")
tomonth = input(12, defval = 12, minval = 01, maxval = 12, title = "To Month")
fromday = input(01, defval = 01, minval = 01, maxval = 31, title = "From Day")
today = input(31, defval = 31, minval = 01, maxval = 31, title = "To Day")

//RSI
uprsi = rma(max(change(close), 0), rsiperiod)
dnrsi = rma(-min(change(close), 0), rsiperiod)
rsi = dnrsi == 0 ? 100 : uprsi == 0 ? 0 : 100 - (100 / (1 + uprsi / dnrsi))
uplimit = 100 - rsilimit
dnlimit = rsilimit
rsidn = rsi < dnlimit ? 1 : 0
rsiup = rsi > uplimit ? 1 : 0
rsidnok = sma(rsidn, rsibars) == 1
rsiupok = sma(rsiup, rsibars) == 1

//Body Filter
body = abs(close - open)
abody = sma(body, 10)
openbodyok = body >= abody / 100 * openbody or useobf == false
closebodyok = body >= abody / 100 * closebody or usecbf == false

//Color Filter
bar = close > open ? 1 : close < open ? -1 : 0
gbar = bar == 1 ? 1 : 0
rbar = bar == -1 ? 1 : 0
opengbarok = sma(gbar, openbars) == 1 or useocf == false
openrbarok = sma(rbar, openbars) == 1 or useocf == false
closegbarok = sma(gbar, closebars) == 1 or useccf == false
closerbarok = sma(rbar, closebars) == 1 or useccf == false

//Signals
//pyrok = lots[1] < pyramiding
up = openrbarok and rsidnok and openbodyok 
dn = opengbarok and rsiupok and openbodyok
norma = (rsi > dnlimit and rsi < uplimit)
exit = ((closegbarok and norma) or (closerbarok and norma)) and closebodyok

//Alert Conditions for Email/SMS notification
//RSI Signal
rsiBuySignal = up and (time > timestamp(fromyear, frommonth, fromday, 00, 00) and time < timestamp(toyear, tomonth, today, 23, 59)) ? 1 : 0
rsiSellSignal = dn and (time > timestamp(fromyear, frommonth, fromday, 00, 00) and time < timestamp(toyear, tomonth, today, 23, 59)) ? -1 : 0

plotarrow(rsiBuySignal, colorup=green,  transp=20, maxheight=20)
plotarrow(rsiSellSignal, colordown=red,  transp=20, maxheight=20)
```
