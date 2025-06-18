# hyponatremia-glideslope

### Medical Context
Hyponatremia is a common but potentially serious electrolyte disturbance among hospitalized patients. Correcting hyponatremia too rapidly can lead to serious complications such as [Central pontine myelinolysis (CPM)](https://en.wikipedia.org/wiki/Central_pontine_myelinolysis) and Osmotic Demyelination Syndrome (ODS). Avoiding these complications requires careful monitoring and titration of therapies. One challenge is defining precise sodium correction goals and determining if the correction is occuring as intended. This is fundamentally a data visualization problem, that can be solved with an interactive web-app.

### Solution
The patient's sodium values are displayed in an xy plot versus time. The safe rate of correction is shown as a ***glideslope***. The user can add more sodium values and see if they within the safe rate of correction. Values outside the safe correction range are highlighted. 
The user can use the default rate of sodium correction (6-8 mEq/L/day), a more cautious rate (4-6 mEq/L/day), a more aggressive rate (8-10 mEq/L/day), or even customize the rate.

### Implementation
Uses [luxon.js](https://moment.github.io/luxon/#/) for handling date/times and [chart.js](https://www.chartjs.org/docs/latest/charts/line.html) for display
Uses [the Asynchronous Clipboard API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API) for pasting and the [window.print function](https://developer.mozilla.org/en-US/docs/Web/API/Window/print) for printing or export to PDF.

### Limitations
Even patients with relatively slow correction of hyponatremia can develop CPM/ODS.
https://evidence.nejm.org/doi/full/10.1056/EVIDoa2300107


### License
MIT License - free to reuse (including commercially) but please acknowledge where you got it!

###
