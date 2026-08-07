# Ver 1.0
What I had in mind was OS curves of a typical oncology drug in a non-curative setting (i.e. metastatic disease). Together with PFS curves, utilities and dosing and cost information these are often used to inform a Partitioned Survival Model (PSM) in health economic evaluations. In a curative setting the modelling may be a bit different. 

The first step is to focus on modelling life-years gained, which is arguably the most important driver of the final result in a PSM. In addition, mechanistic cost calculations does not provide such artistic satisfaction, which is needed during the summer holiday.

While the app is created using the genAI tools of one of the tech giants the app itself is deterministic and even somewhat transparent. These coding tools are proprietary and I purchased the access to them myself. I also run the tools using my private laptop. All the examples and other data are available from public web and found by AI. As a further disclaimer, the purpose of this study is to map AI's capabilities rather than to provide guide for modelling.

In practice the code is R using interactive Shiny UI. Conversion of digitized point to patient level data is performed using the R package IPDfromKM: https://doi.org/10.1186/s12874-021-01308-8. Curve tracing logic is a code written by AI.
As always there are various options with R / Python, but aforementioned package was proposed by AI. 

### Step 1: get a Kaplan-Meier figure of overall survival (OS) & load the figure into the app
preferably a good quality, high resolution figure of decent size.
You may download it as a JPEG, PNG or another image format. In practice you may need to save captured image into your computer and use the option **Upload Local Image** instead of option **Load from URL**. The latter option needs more refinement as URLs typically contain other content besides the target image. Using a standard screenshot capture tool You can set the boundaries manually, save the image temporarily to Your computer and upload from there. Please, include the at-risk table below the figure as it is also utilized with Optical Character Recognition (OCR) feature.

The app allows comparison of two curves from the same figure. That is, the comparison is limited to the treatment and control groups within the same trial, and indirect comparisons are not possible at the moment. The sample figure is automatically generated for illustration. 

<img width="303" height="354" alt="loading" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/lataus.png" />

 
### Step 2: calibrate the figure
if automatic calibration fails perform it manually by setting origin, X-max and Y-max using the mouse.**Please, set also the X max value (the box on the left) manually according to your image - this value is important for the calculations as the tick marks are considered relative to maximum!**

<img width="1442" height="600" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/calibration.png" />

### Step 3: Trace the curves
Use automatic curve tracing or do it manually. Select the active curve (treatment / control) from the dropdown menu and then trace it by selecting some point on the curve with your mouse. The autotrace should search the points along the curve. The autotrace implements a property of Kaplan-Meier (K-M) curves: when x increases y is non-increasing i.e. it follows the monotonicity property. I explicitly prompted the AI to implement this and had an argument with AI until I realized that rows are numbered from top to bottom. 

If you are not satisfied with the result, retrace the curves and try again. Sometimes several attempts are required to achieve the desired result. I added a zoom  and merge-stitch features to tracking. (Zoom needs to be refined!) The latter allows one to keep the part of the curve where tracking was successful while trying again remaining parts. In principle, even point-wise manual tracking is possible. Also erase function was added to remove tails or other incorrectly "tracked" excess points.

<img width="1383" height="631" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/track_curves.png" />


### Step 4: Reconstruct IPD - this step must be completed before moving on.
From the previous traced K-M curves individual patient data (IPD) are recreated. This data is then used for modelling.
Please specify also the number of patients at the baseline and numbers at-risk at different time points. This information usually appears as numbers in the figure and at-risk table below the image. Click the **auto-extract at-risk (OCR)** button for performing OCR search of these numbers. However, if this information is not automatically extracted it should be manually fed. Otherwise, information will be lost even if the K-M curve visually appears similar to the original one. So, please verify the result from OCR and fix if necessary.
Finally, click **Run IPD reconstruction** and admire figures accompanied by fancy statistics.

<img width="390" height="600" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/at-risk_tables.png" />

### Step 5: Fit standard parametric models
Standard parametric models are fitted to the reconstructed IPD and extrapolated. You may set the extrapolation horizon. The default is 120 and the assumption is that the time units are in months (i.e. 120 corresponds to 10 years). AIC and BIC values are shown to describe the statistical fit of each model. No flexible spline or other more complex models are available thus far.

You may apply tail trimming when fitting the models. Typically the tail of the K-M curve can have a large effect on model fit and therefore also extrapolation. However, the shape of the tail may be based on a small number of patients at risk. The tail of the K-M curve may be "trimmed" and excluded from model fitting. The effect of tail trimming can be evaluated in the next sheet. I did not consider more complicated methods such as weighting here. 

<img width="1775" height="755" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/fits.png" />

### Step 6: Choose the best model for inspection - combined model comparison
Choose one of the models for more detailed inspection. It doesn't have to be the same for the treatment and for the control. However, choosing different models for treatment and control typically requires additional justification. As we are interested about the difference between arms we do not want base our results to different **assumptions** about the distribution of arrival times. The figure is simplified as just the chosen models with their extrapolations are displayed alongside with the "raw" K-M curve.

You may also study the (smoothed) raw hazards, based on reconstructed IPD, and compare them to the hazards implied by the model. Obviously, these two should match together. You may "zoom" the hazard plot by adjusting the x-axis scaler with the slider input. There is no zoom feature for Y-axis, but I noticed sometimes this would be beneficial. Also, note that treatment hazard is usually expected to smaller than the hazard in the control arm - so be careful with the colorcoding while interpreting the results.

<img width="971" height="742" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/final_res.png" />

Now you can see some statistics calculated. How many life years are gained over the extrapolation horizon in AUC interpretation (both undiscounted and discounted). You may set the discount rate. Life years gained are baseed on difference in the Restricted Mean Survival Time RMST: 
```math
RMST(\tau)=\int_0^\tau S(t)\,dt
```

where the t refers to extrapolation horizon length and S is the survival function. Thus RMST is the area under survival curve up to the extrapolation horizon. Restricted refers to the fact that not all events or censoring have been observed during the horizon (Y>0 in the curve). Also the difference in modelled medians is displayed (note that the medians are based on fitted model - not raw data).

<p align="center">
  <img src="https://raw.githubusercontent.com/aihyvari/LY_extraplolation/main/kuvat/LYs.png" width="300" alt="Life years">
  <img src="https://raw.githubusercontent.com/aihyvari/LY_extraplolation/main/kuvat/HRs.png" width="300" alt="Hazard ratios">
</p>

In general discussions it is often stated that just a few months can be gained with the new treatment. With IO treatments it can happen that many patients or even majority of them have negligible benefit, but some share 20-40 % can have very long lasting effect. So one can either have significant benefit or almost none at all. This is particularly evident in PFS curves, where the delayed curve separation is more clear. This kind of setting leads to a situation where difference in RMST can be substantially larger than the difference in the OS medians.  

Implicit assumption with extrapolation is that there are no new drivers of the result after observations end. At least not until something like natural mortality is introduced into the model as upper limit of survival. Basically this often means that if the treatment and control curves are separated during observational period they will remain similarly separated during extrapolation period. AUC difference increases as the extrapolation horizon lengthens. 

Obviously these conclusions are based on simplification & generalization. 

## Here are some other random real examples:

1.The figures above were based on **OAK: A Phase 3 efficacy trial in second-line non-squamous or squamous advanced/metastatic NSCLC**. https://tecentriq.global/home/indication/non-small-cell-lung-cancer.html 
Here origin, x-max and y-max had to given as well as 27 value for x-max. Choosing log-logistic for both treatment and control curves prevents curves from crossing like above. Moreover it produces Cox-HR 0.74 which matches the original. 
<img width="971" height="742" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/ATE_res2.png" />
In the NICE assessment TA520: *"the company used Kaplan–Meier data up to 3 months and extrapolated the data using a log-logistic curve based on best statistical fit for both atezolizumab and docetaxel. Committee.... concluded that using the Kaplan–Meier data with a log-logistic curve was appropriate for its decision-making."* https://www.nice.org.uk/guidance/ta520/documents/final-appraisal-determination-document

**2. Second example**. Pembrolizumab vs platinum chemotherapy KEYNOTE-024 OS figure from: https://ascopubs.org/doi/pdf/10.1200/JCO.21.00174. Figure calibration is successful. Treatment curve tracking looks nice in one go, but the control curve needs to be done in two pieces. There are no problems in building at-risk tables and IPD. The Weibull model gives the best fit for the treatment curve, but the log-normal for the control curve. The fitted Weibull curve diverges visibly from original K-M at the median. Choosing log-logistic model for both treatment and control gives a good visual fit and relatively nice AIC and BIC. Seems that the curves remain separated at the end of horizon and thus the difference in the RMST will increase if extrapolation horizon is extended. Cox HR was 0,64 while it was reported 0,62 in the original publication. From the table you may observe that the HR is not constant over time. This is general result and you may study the HR in different time points.

In the NICE assessment TA531: *"The committee noted that the company's choice of exponential extrapolation to model overall survival... The ERG stated that the uncertainty around the overall survival extrapolation even at 2 years is the main source of uncertainty in the cost effectiveness analyses, but the company's approach was plausible."* https://www.nice.org.uk/guidance/ta531/resources/pembrolizumab-for-untreated-pdl1positive-metastatic-nonsmallcell-lung-cancer-pdf-82606895901637
Having now longer follow-up data at hand it seems that exponential distribution does not fit as well as log-logistic one. Moreover, it was back then standard to use piecewise approach: at first K-M data was used, which was continued by exponential distribution.
<img width="971" height="742" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/Pembro_res2.png" />

3. **Crossing curves: Nivolumab vs docetaxel**
 in Advanced Non-squamous NSCLC (CheckMate 057) the OS curves cross implicating non-proportinal hazards. https://www.nejm.org/doi/full/10.1056/NEJMoa1507643
Here you need to manually calibrate the figure as autocalibration failed. Curve tracing worked seemingly nicely at the first go. I just added one point near y-axis manually. OCR for at-risk table failed. Basically nivolumab numbers appeared in the control while control remained empty. I fixed this manually. Again the best statistical fit for treatment and that for the control are from different distributions. In this case it seems that none of the parametric models can replicate two second peak visible in the hazard plot of the treatment arm. In addition, the number at-risk falls in the end so that perhaps tail-trimming is necessary. I did try to trim from where under 10% remained at-risk. Still the parametric models can not replicated the hazard shape.
<img width="700" height="500" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/Nivo_res.png" />

NICE TA713: https://www.nice.org.uk/guidance/ta713/chapter/3-Committee-discussion#economic-model
"The company's log-normal model for overall survival and scenario analysis using a spline 3-knot model for nivolumab are both plausible"

# Ver 2.0 add some genAI
The end results of this app can be consired as building blocks of PSM. Progression Free Survival (PFS) curves could be handled as OS curves.
1. GenAI to perform screen capture automatically? Now one need to get around cloudflare etc.
2. GenAI to extract dosing regimen information from SmPCs?
3. GenAI to extract comparative assessment results from Fimea, NICE etc.
