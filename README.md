# hyponatremia-glideslope

### 🏥 Medical Context
Hyponatremia is a common but potentially serious electrolyte disturbance among hospitalized patients. Correcting hyponatremia too rapidly can lead to serious complications such as [Central pontine myelinolysis (CPM)](https://en.wikipedia.org/wiki/Central_pontine_myelinolysis) and Osmotic Demyelination Syndrome (ODS). Avoiding these complications requires careful monitoring and titration of therapies. One challenge is defining precise sodium correction goals and determining if the correction is occuring as intended. This is fundamentally a data visualization problem, that can be solved with an interactive web-app.

### 📈 Solution
The patient's sodium values are displayed in an xy plot versus time. The safe rate of correction is shown as a ***glideslope***. The user can add more sodium values and see if they within the safe rate of correction. Values outside the safe correction range are highlighted. 
The user can use the default rate of sodium correction (6-8 mEq/L/day), a more cautious rate (4-6 mEq/L/day), a more aggressive rate (8-10 mEq/L/day), or even customize the rate.
Once the user has built the visualization they can either review it via the web app, copy it to the clipboard (to paste into the EHR), or print it out.
The user can also indicate when treatments such as D5W, Normal Saline, 3% saline, or ddAVP are administered. These are also shown on the graph.
Finally, the user can paste the results graph into the medical record or print it out, so everyone on the team has shared mental model about the plan for safe sodium correction.

![](https://github.com/nickmmark/hyponatremia-glideslope/blob/main/sodium_correction_v1.gif)

### ⚙️ Implementation
- Uses [luxon.js](https://moment.github.io/luxon/#/) for handling date/times and [chart.js](https://www.chartjs.org/docs/latest/charts/line.html) for displaying the results. It uses the Luxon [Chart.js adapter](https://github.com/chartjs/chartjs-adapter-luxon) for easy interoperability between the two.
- Uses [the Asynchronous Clipboard API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API) for pasting and the [window.print function](https://developer.mozilla.org/en-US/docs/Web/API/Window/print) for printing or export to PDF.

### ⚠️ Limitations
Although the [original report of osmotic demyelination syndrome](https://www.nejm.org/doi/abs/10.1056/NEJM198606123142402) suggested that the cause was **rapid** correction of sodium (>12 mEq/L/day) more recent studies suggest that ODS can occur with slower rates of correction. In a [cohort described by Seethapathy _et al_](https://evidence.nejm.org/doi/full/10.1056/EVIDoa2300107), CPM occured in 5 of 7 patients despite correcting sodiuma at a slower rate (<8 mEq/L/day).
Even patients with relatively slow correction of hyponatremia can develop CPM/ODS. A larger [multi-center observational study by MacMillan et al](https://evidence.nejm.org/doi/full/10.1056/EVIDoa2200215) found that 7/12 patients who developed CPM did not have rapid correction. Thus, extremely cautious rates of sodium correction (e.g. <6 mEq/L/day) may be warranted in high risk patients. 

### 🔢 Versions
- 1.0 first working version - features sodium graph with variable rates of correction
- 1.1 added ability to print, save, & copy the graph
- 2.0 added basic therapies

### 🚧 Features to Add
[x] Add therapies (d5W, 3% NS, ddAVP, etc)
[ ] Add more info about therapies on the graph, for example:
- ideally I could add a weight based calculator to *estimate* how much a given free water bolus would be expected to decrease the sodium
- when was ddAVP given --> show the expected half life of the drug

[ ] Make it possible to save the graph or share with someone else
- one approach would be to persist the data (e.g. on a server) and have a login or direct link
- alternatively could have a "share" button that included the data in the hyperlink itself?

[ ] Add a watermark to the graph

[ ] Ideally this would be integrated into the EHR so the user wouldn't have to do any data entry.
- this could pull lots of additional data --> therapeutics given, urine output, other labs, etc
- this is really where this app needs to go ultimately & why I've made it open source under an MIT license

[ ] Need a better name! 

### 🪪 License
MIT License - free to reuse (including commercially) but please acknowledge where you got it!

### 📚 References
- Sterns RH _et al_, [*Osmotic demyelination syndrome following correction of hyponatremia*](https://www.nejm.org/doi/abs/10.1056/NEJM198606123142402) _NEJM_ 1986 Jun 12;314(24):1535-42. - Original report of describing Osmotic Demyelination Syndrome
- MacMillan TE _et al_, [Osmotic Demyelination Syndrome in Patients Hospitalized with Hyponatremia](https://evidence.nejm.org/doi/full/10.1056/EVIDoa2200215). _NEJM Evidence_ 2023 Apr;2(4):EVIDoa2200215
- Seethapathy H _et al_, [Severe Hyponatremia Correction, Mortality, and Central Pontine Myelinolysis.](https://evidence.nejm.org/doi/full/10.1056/EVIDoa2300107) _NEJM Evidence_ 2023 Oct;2(10):EVIDoa2300107
