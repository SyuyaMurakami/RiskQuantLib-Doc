With QuantLib
====================

.. toctree::
   :maxdepth: 4

Once upon a time, there is a famous ask in Stack Overflow:

   `Why it is so complicated when using QuantLib for option pricing? Even if I just want to price a vanilla option, it takes several lines to do it.`

It is true, if you use QuantLib to price a vanilla call option, you may do it like:
::

   # Set Evaluation Date
   today = ql.Date(18, 3, 2021)
   ql.Settings.instance().evaluationDate = today

   # Build An Option
   # Set Payoff
   payoff=ql.PlainVanillaPayoff(ql.Option.Call, 100.0)

   # Set Exercise Date
   europeanExercise=ql.EuropeanExercise(ql.Date(18, 11, 2021))
   option = ql.EuropeanOption(payoff, europeanExercise)

   # Set Initial Stock Price, Risk-Free Rate, Volatility
   u = ql.SimpleQuote(100.0)      # Initial Stock Price
   r = ql.SimpleQuote(0.05)       # Risk-Free Rate
   sigma = ql.SimpleQuote(0.20)    # Volatility

   # Set Term Structure Of Curve
   riskFreeCurve = ql.FlatForward(0, ql.TARGET(), ql.QuoteHandle(r), ql.Actual360())
   volatility = ql.BlackConstantVol(0, ql.TARGET(), ql.QuoteHandle(sigma), ql.Actual360())

   # Initialize Stochastic Process And Pricing Enging
   process = ql.BlackScholesProcess(ql.QuoteHandle(u),
                                 ql.YieldTermStructureHandle(riskFreeCurve),
                                 ql.BlackVolTermStructureHandle(volatility))
   engine = ql.AnalyticEuropeanEngine(process)

   # Set Enging To Option
   option.setPricingEngine(engine)

   # Print Result
   print(f'Option Value：{option.NPV():.4f}')
   print("%-12s: %4.4f" %("Delta", option.delta() ))
   print("%-12s: %4.4f" %("Gamma", option.gamma() ))
   print("%-12s: %4.4f" %("Theta", option.vega()))

However, what if you have ten options and you want to price them all? What if you just want a test that show how your option value will be in certain market situation? For research purpose or you are a real derivative trader, you may sometimes have a table looks like:

+----------+------------+------------+------------+----------+------------+-----+
|OptionCode|   PayOff   |ExerciseType|ExerciseDate|StockPrice|RiskFreeRate|Sigma|
+==========+============+============+============+==========+============+=====+
|     A    |PlainVanilla|  European  | 2021-09-20 |   100.0  |     0.05   | 0.20|
+----------+------------+------------+------------+----------+------------+-----+
|     B    |PlainVanilla|  European  | 2022-03-20 |    97.6  |    0.032   | 0.17|
+----------+------------+------------+------------+----------+------------+-----+
|   ...    |    ...     |    ...     |    ...     |    ...   |    ...     | ... |   
+----------+------------+------------+------------+----------+------------+-----+




