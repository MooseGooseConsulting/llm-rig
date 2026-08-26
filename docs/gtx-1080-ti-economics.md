# GTX 1080 Ti economics

This is a targeted evaluation document, not default context for ordinary serving work.

The GTX 1080 Ti qualifies only if it has a practical role relative to a suitable API alternative. Raw token rate alone is not the decision.

~~~text
local cost per million useful output tokens
  = ((measured wall watts / 1000 × electricity price per kWh)
      / measured useful output tokens per hour)
    × 1,000,000
~~~

The evaluation records:

- whole-system wall power and idle baseline, so the power-cost basis is explicit;
- useful output tokens per hour, not merely a synthetic decode rate;
- task quality and latency on the workload the card would actually serve;
- the current API cost for a model good enough for that workload.

If local serving is slower, lower quality, and more expensive, the card does not qualify as a practical inference surface merely because it can generate text.
