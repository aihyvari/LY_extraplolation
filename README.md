# Ver 1.0
What I had in my mind was OS curves of a typical oncology drug in a non-curative setting (i.e. metastatic disease). Together with PFS curves, utilities and dosing and cost information these are often used to inform a Partitioned Survival Model (PSM) in health economic evaluations. In curative setting the modelling may be a bit different. 

The first step is to focus on modelling life years gained, which is arguably the most important driver of the final result in the PSM. In addition, mechanistic cost calculation does not provide such artistic satisfaction, which is needed during the summer holiday.

While the app is created using the genAI tools of one of the tech giants the app itself is deterministic and even somewhat transparent. These coding tools are proprietary and I purchased the access to them myself. I also run the tools using my private laptop. All the examples and other data are available from public web and found by AI performing searches. In practice the code is R using interactive Shiny UI. Digitization is performed using the R package IPDfromKM: https://doi.org/10.1186/s12874-021-01308-8.
As always there are various options with R / Python, but aforementioned package was proposed by AI. 

### Step 1: get a Kaplan-Meier figure of overall survival OS & load the figure into the app
preferably a good quality, high resolution figure of decent size.
You may download it as a JPEG, PNG or another image format. In practise you may need to save captured image into your computer and use the option **Upload Local Image** instead of option **Load from URL**. The latter option needs more refinement as URLs typically contain other content besides the target image. Using a standard screenshot capture tool You may set the boundaries manually, save the image temporarily to Your compure and upload from there. Please, include the at-risk table below the figure as it is also utilized with Optical Character Recognition (OCR) feature.

The app allows comparison of two curves from the same figure. That is, the comparison is limited to the treatment and control groups within the same trial, and indirect comparisons are not possible at the moment. The sample picture is automatically generated for illustration. 

<img width="303" height="354" alt="loading" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/lataus.png" />

 
### Step 2: calibrate the figure
if automatic calibration fails do it manually by setting origin, X-max and Y-max by mouse.**Please, set also the X max value (the box on the left) manually according your image - this important for the calculations as the tick marks are considered relative to maximum!**

<img width="1442" height="600" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/calibration.png" />

### Step 3: Trace the curves
Use automatic curve tracing or do it manually. Choose the active curve (treatment / control) from the dropdown menu and then trace it by selecting some point on the curve with your mouse. The autotrace should search the points along the curve. The autotrace implements a property of Kaplan-Meier (K-M) curves: when x increases y is non-increasing i.e. it follows the monotonicity property (at least I explicitely prompted the AI to implement this). If you are not satisfied with the result, retrace the curves and try again. Sometimes several attempts are required to achieve the desired result. I added a zoom feature and merge & stich feature to tracking. The latter allows one to keep the part of the curve where tracking was successful while trying again remaining parts. In principle, even point-wise manual tracking is possible. Also erase function was added for removing tails or other excess "tracked" points.

<img width="1383" height="631" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/track_curves.png" />


### Step 4: reconstruct IPD - this step must be completed before moving on.
From the previous traced K-M curves indivial patient data (IPD) are recreated. This data is then used for modelling.
Please specify also the number of patients at the baseline and at-risk numbers at the different time points. This information usually appears as a number in the figure and at-risk table below the image. Click the **auto-extract at-risk (OCR)** button for performing OCR search of these numbers. However, if this information is not automatically extracted it should be manually fed. Otherwise, information will be lost even if the K-M curve visually appears similar to original one. So, please verify the result from OCR and fix if necessary.
Finally click **Run IPD reconstruction** and admire figures accompanied by mysterious statistics.

<img width="390" height="600" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/at-risk_tables.png" />

### Step 5: fit standard parametric models
Standard parametric models are fitted to the IPD and extrapolated. You may set the extrapolation horizon. The default is 120 and the assumption is that the time units are in months (i.e. 120 corresponds to 10 years). AIC and BIC values are shown to describe the statistical fit of each of models. No flexible spline or other more complex models are avaibable thus far.

You may implement tail trimming when fitting the models. Typically the tail of the K-M curve can have a large effect on model fit and therefore also extrapolation. However, the shape of the tail may be based on small number of patients at risk. The tail of K-M curve may be "trimmed" and left out from model fitting. The effect of tail trimming can be evaluated in the next sheet. I did not consider more complicated methods such as weighting here. 

<img width="1775" height="755" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/kuvat/fits.png" />

### Step 6: choose the best model for inspection - combined model comparison
Choose one of the models for more detailed inspection. It doesn't have to be the same for the treatment and for the control. However, choosing different models for treatment and control typically requires additional justification. The figure is simplified as just the chosen models with their extrapolations are displayed alongside with the "raw" K-M curve.

You may also study the (smoothed) raw hazards, based on reconstructed IPD, and compare them to the hazards implied by the model. Obviously, these two should match together. You may "zoom" the hasard plot by adjusting the x-axis scaler with the slider input. There is no zoom feature for Y-axis, but I noticed sometimes this would be beneficial.

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

1.The curve above was 
2. Pembrolizumab vs platinum chemotherapy KEYNOTE-024: (https://ascopubs.org/doi/pdf/10.1200/JCO.21.00174)
3. EX2
More about these below...

# Ver 2.0 add some genAI
The end results of this app can be consired as building blocks of PSM. Progression Free Survival (PFS) curves could be handled as OS curves.
1. GenAI to extract dosing regimen information from SmPCs?
2. GenAI to extract comparative assessment results from Fimea, NICE etc.
