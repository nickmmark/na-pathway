# NaPathway: A tool for safely correcting hyponatremia

### 🧠 Medical Context for Hyponatremia
Severe hyponatremia (sodium < 120 mEq/L) is a potentially serious electrolyte disturbance among hospitalized patients. Correcting hyponatremia too rapidly can lead to serious neurologicla complications such as [Central pontine myelinolysis (CPM)](https://en.wikipedia.org/wiki/Central_pontine_myelinolysis) and Osmotic Demyelination Syndrome (ODS). Avoiding these complications requires careful monitoring and titration of therapies. One challenge is defining precise sodium correction goals and determining if the correction is occuring as intended, particularly in the context of multiple therapies being administered. This is fundamentally a data visualization problem, that can be solved with an interactive web-app.


### 📈 Solution
The patient's sodium values are displayed in an xy plot versus time. The safe rate of correction is shown as a ***glideslope***. The user can add more sodium values and see if they fall within the safe rate of correction. Values outside the safe correction range are highlighted. 
The user can use the default rate of sodium correction (6-8 mEq/L/day), a more cautious rate (4-6 mEq/L/day), a more aggressive rate (8-10 mEq/L/day), or even customize the rate.
The user can also indicate when treatments such as D5W, Normal Saline, 3% saline, or ddAVP are administered. These therapies are also shown on the graph. The app can also help the user estimate the effects of giving saline or free water.
Finally, the user can save the graph to disk, paste it into the medical record, or print it out, so everyone on the medical team has shared mental model about the plan for safe sodium correction.

![](https://github.com/nickmmark/hyponatremia-glideslope/blob/main/sodium_correction_v1.gif)

<br>

### 🧮 Calculating the expected change in sodium with IV fluids
In order to calculate how an infusion or bolus will alter the patients serum sodium, we need to estimate their [**Total Body Water (TBW)**](https://en.wikipedia.org/wiki/Body_water). We can estimate TBW using:
```math
TBW = Mass(kg) x C
```

The coefficient (C) used to estimate TBW depends on age, gender, and weight:
- Children and adult males: 0.6 * body weight (in kg)
- Adult females and elderly males: 0.5 * body weight (in kg)
- Elderly females: 0.45 * body weight (in kg)

Note that there are more accurate anthropometric equations - like the [Watson Formula](https://pubmed.ncbi.nlm.nih.gov/6986753/) - that use height, weight, age, and gender. I have chosen to use the simpler approximation because height data is not always available. Ideally, if this was connected to the EHR, the app would use the better formula if more information was available.

In order to calculate the **expected change in sodium**, we also need to consider the sodium content of different intravenous fluid solutions:
- 0.9% saline = 154 mEq/L
- 3% saline = 513 mEq/L
- Lactated Ringers = 130 mEq/L
- Dextrose 5% water (D5W) = 0 mEq/L

Specifically to calculate the *expected change* in sodium, we use the patients TBW, most recent sodium value, and the sodium content of the infusate, using the following equation:

```math
\Delta[\mathrm{Na}]
=
\frac{[\mathrm{Na}]_{\text{infusate}}
      -[\mathrm{Na}]_{\text{serum}}
     }{\mathrm{TBW}+\mathrm{volume_infused}}
```

It's slighty more complicated if we want to calculate the hourly change in sodium with continuous infusions. 

Given:

- $TBW$ = total body water (L)  
- ${Na}_{start}$ = initial serum sodium (mEq/L)  
- ${Na}_{inf}$ = sodium concentration of the infusate (mEq/L)  
- $\dot V\$ = infusion rate (mL/hr)  
- ${V}_{hr}$ = $\dot{V} / 1000$ (L infused in one hour)  

Then after one hour:

```math
\mathrm{NewNa}_{\mathrm{hr}}
= \frac{[\mathrm{Na}]_{\mathrm{start}}\times \mathrm{TBW}
       + [\mathrm{Na}]_{\mathrm{inf}}\times V_{\mathrm{hr}}}
      {\mathrm{TBW} + V_{\mathrm{hr}}}
```

and the expected change per hour is

```math
\Delta[\mathrm{Na}]_{\mathrm{per\,hr}}
= \mathrm{NewNa}_{\mathrm{hr}} \;-\; [\mathrm{Na}]_{\mathrm{start}}.
```



Practically, we can surface the ***expected change in sodium*** as a property of each infusion (as a tooltip on each therapy). Therefore if a patient is receiving multiple treatments we can see how *each* would be expected to change their serum sodium.

<br><br>


### ⚙️ Implementation
- Uses [luxon.js](https://moment.github.io/luxon/#/) for handling date/times and [chart.js](https://www.chartjs.org/docs/latest/charts/line.html) for displaying the results. It uses the Luxon [Chart.js adapter](https://github.com/chartjs/chartjs-adapter-luxon) for easy interoperability between the two.
- Uses [the Asynchronous Clipboard API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API) for pasting and the [window.print function](https://developer.mozilla.org/en-US/docs/Web/API/Window/print) for printing or export to PDF.


<br><br>


### ⚠️ Limitations
#### Sodium does not fully explain ODS risk
Although the [original report of osmotic demyelination syndrome](https://www.nejm.org/doi/abs/10.1056/NEJM198606123142402) suggested that the cause was **rapid** correction of sodium (>12 mEq/L/day) more recent studies suggest that ODS can occur with slower rates of correction. In a [cohort described by Seethapathy _et al_](https://evidence.nejm.org/doi/full/10.1056/EVIDoa2300107), CPM occured in 5 of 7 patients despite correcting sodiuma at a slower rate (<8 mEq/L/day).
Even patients with relatively slow correction of hyponatremia can develop CPM/ODS. A larger [multi-center observational study by MacMillan et al](https://evidence.nejm.org/doi/full/10.1056/EVIDoa2200215) found that 7/12 patients who developed CPM did not have rapid correction. Thus, extremely cautious rates of sodium correction (e.g. <6 mEq/L/day) may be warranted in high risk patients. 
The NaPathway app allows users to choose a more cautious rate of correction for high risk patients. This is not a substitute for clinical judgement. 

#### Changes in endogenous ADH can lead to over-correction
Frequently, patient present with hyponatremia in the setting of high ***endogenous*** ADH levels (for example with hypovolemia). Following volume resuscitation, the endogenous ADH levels drop and the sodium can rise rapidly, potentially causing dangerously rapid overocorrection. For this reason ***exogenous*** ADH can be administered in the form of ddAVP to blunt the rapid rise in sodium when endogenous ADH falls. This apporach is termed the ***ddAVP clamp***. A good discussion of this approach can be found [here](https://emcrit.org/pulmcrit/taking-control-of-severe-hyponatremia-with-ddavp/). The NaPathway app allows you to add ddAVP on the plot to see when it is working.

#### Need for rapid initial correction
In some circumstances (for example seizures), a rapid initial sodium correction is necessary. Typically the goal will be to rapidly increase sodium by 3-5 mEq/L, however the 24 hour correction goal remains unchanged. The current NaPathway app does not adjust the correction glideslope to account for this.

<br><br>


### 🔢 Versions
- 1.0 first working version - features sodium graph with variable rates of correction
- 1.1 added ability to print, save, & copy the graph
- 2.0 added basic therapies
- 3.0 implemented calculations of TBW and expected change in serum sodium

<br><br>


### 🚧 Features to Add
[x] Add therapies (d5W, 3% NS, ddAVP, etc)

[x] Add more info about therapies on the graph, for example:
- ideally I could add a weight based calculator to *estimate* how much a given free water bolus would be expected to decrease the sodium
- when was ddAVP given --> show the expected half life of the drug

[ ] Make it possible to easily share the graph with someone else
- one approach would be to persist the data (e.g. on a server) and have a login or direct link
- alternatively could have a "share" button that included the data in the hyperlink itself?

[ ] Add a watermark to the graph
- always good to be be transparent about the source! also helps others find the app.

[ ] Ideally this would be integrated into the EHR so the user wouldn't have to do any data entry.
- this could pull lots of additional data --> therapeutics given, urine output, other labs, etc
- this is really where this app needs to go ultimately & why I've made it open source under an MIT license

<br><br>


### 🪪 License
NaPathway is released under an [MIT License](https://opensource.org/license/mit) - free to reuse (including commercially) but please acknowledge where you got it!
It's my sincere hope that people **will** steal this code and use it to deploy within thier own EHR. 

<br><br>

### 📚 References
- Sterns RH _et al_, [*Osmotic demyelination syndrome following correction of hyponatremia*](https://www.nejm.org/doi/abs/10.1056/NEJM198606123142402) _NEJM_ 1986 Jun 12;314(24):1535-42. - Original report of describing Osmotic Demyelination Syndrome
- MacMillan TE _et al_, [Osmotic Demyelination Syndrome in Patients Hospitalized with Hyponatremia](https://evidence.nejm.org/doi/full/10.1056/EVIDoa2200215). _NEJM Evidence_ 2023 Apr;2(4):EVIDoa2200215
- Seethapathy H _et al_, [Severe Hyponatremia Correction, Mortality, and Central Pontine Myelinolysis.](https://evidence.nejm.org/doi/full/10.1056/EVIDoa2300107) _NEJM Evidence_ 2023 Oct;2(10):EVIDoa2300107
- Adrogué HJ, Madias NE. [Hyponatremia.](https://www.nejm.org/doi/10.1056/NEJM200005253422107?url_ver=Z39.88-2003&rfr_id=ori:rid:crossref.org&rfr_dat=cr_pub%20%200pubmed) _NEJM_ 2000 May 25;342(21):1581-9
