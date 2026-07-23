# Ver 1.0
What I had in my mind was OS curves of a typical oncology drug in a non-curative setting (i.e. metastatic disease). Together with PFS curves, utilities and dosing and cost information these are often used to inform a Partitioned Survival Model (PSM) in health economic evaluations. In curative setting the modelling may be a bit different. 

The first step is to focus on modelling life years gained, which is arguably the most important driver of the final result in the PSM. Mechanistic cost calculation does not provide such artistic satisfaction, which is needed during the summer holiday.

While the app is created using the genAI tools of one of the tech giants the app itself is deterministic. These tools are proprietary and I purchased the access to them myself. I also run the tools using my private laptop. In practice the code is R using interactive Shiny UI. Digitization is performed using the package IPDfromKM: https://doi.org/10.1186/s12874-021-01308-8.
As always there are various options with R / Python, but aforementioned package was proposed by AI. 

### Step 1: get a Kaplan-Meier figure of overall survival OS & load the figure into the app
preferably a good quality, high resolution figure of decent size.
You may download it as a JPEG, PNG or another image format. In practise you may need to save captured image into your computer and use the option **Upload Local Image** instead of option **Load from URL**. The latter option needs more refinement as URLs typically contain other content besides the target image. Using a standard screenshot capture tool You may set the boundaries manually, save the image temporarily to Your compure and upload from there. 

The app allows comparison of two curves from the same figure. That is, the comparison is limited to the treatment and control groups within the same trial, and indirect comparisons are not possible at the moment. The sample picture is automatically generated. Here are some other random real examples:

1. Pembrolizumab vs platinum chemotherapy KEYNOTE-024: (https://ascopubs.org/doi/pdf/10.1200/JCO.21.00174)
2. EX2
More about these below...

<img width="303" height="354" alt="loading" src="https://github.com/aihyvari/LY_extraplolation/blob/main/lataus.png" />

 
### Step 2: calibrate the figure
if automatic calibration fails do it manually by setting origin, X-max and Y-max by mouse.**Please, set the X max value (the box on the left) manually according your image - this important for the calculations as the tick marks are considered relative to max!**

<img width="1442" height="600" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/calibration.png" />

### Step 3: Trace the curves
Use automatic curve tracing or do it manually. Choose the active curve (treatment / control) from the dropdown menu and then trace it by selecting some point on the curve with your mouse. The autotrace should search the points along the curve. The autotrace implements a property of Kaplan-Meier (K-M) curves: when x increases y is non-increasing i.e. it follows the monotonicity property. If you are not satisfied with the result, retrace the curves and try again. Sometimes several attempts are required to achieve the desired result. In principle, even point-wise manual tracking is possible. 

<img width="1383" height="631" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/track_curves.png" />


### Step 4: reconstruct IPD - this step has to be done before moving on.
From the previous traced K-M curves indivial patient data are recreated. This data is then used for modelling.
Please specify also the number of patients in the beginning and at-risk numbers in different time points. This information usually appears as a number in the figure and at-risk table below the image. However, if this information is not automatically extracted it should be manually fed. Otherwise information will be lost even if the K-M curve visually appers similar to original one. So, please check the result from OCR and fix if necessary.

<img width="297" height="311" alt="image" src="https://github.com/user-attachments/assets/e555ff14-9f88-40e7-a763-77dc8e969d59" />


<img width="360" height="85" alt="image" src="https://github.com/aihyvari/LY_extraplolation/blob/main/at-risk_tables.png" />

### Step 5: fit standard parametric models
Standard parametric models are fitted to the IPD and extrapolated. You may set the extrapolation horizon. The default is 120 and the assumption is that the time units are in months (i.e. 120 corresponds to 10 years). AIC and BIC values are shown to describe the statistical fit of each of models. No flexible spline or other more complex models are avaibable thus far.

You may implement tail trimming when fitting the models. Typically the tail of the K-M curve can have a large effect on model fit and extrapolation. However, the shape of the tail may be based on small number of patients at risk. Hence, the tail may be trimmed and left it out from model fitting. The effect of tail trimming can be evaluated in the next sheet. 

<img width="1775" height="755" alt="image" src="https://github.com/user-attachments/assets/ce3fb795-c06d-4cc0-b3fa-2e36cbd2b947" />

### Step 6: choose the best model for inspection - combined model comparison
Choose one of the models (for the treatment and for the control, does not need to be the same) for more detailed inspection. The figure is simplified as just the chosen models with their extrapolations are displayed alongside with the "raw" K-M curve.

<img width="971" height="742" alt="image" src="https://github.com/user-attachments/assets/f402dba8-21a7-4c7f-b9f1-8a261480b207" />

Now you can see some statistics calculated. How many life years are gained over the extrapolation horizon in AUC interpretation (both undiscounted and discounted). This is the Restricted Mean Survival Time RMST: 
```math
RMST(\tau)=\int_0^\tau S(t)\,dt
```

where the t refers to extrapolation horizon length and S is the survival function. Thus RMST is the area under survival curve up to the extrapolation horizon. Restricted refers to the fact that not all events or censoring have been observed during the horizon (Y>0 in the curve). Also the difference in modelled medians is shown.

In general disscussion it is often stated that just a few months can be gained with the new treatment. With IO treatments this statement is somewhat imprecice. Many patients or even majority of them have negligble benefit, but some share 20-40 % can have very long lasting effect. So one can either have significant benefit or almost none at all. This kind of setting leads to a situation where RMST can be significantly larger than the difference in the medians. Implicit assumption with extrapolation is that there are no new drivers of thge result after observations end. Basically this often means that if the treatment and control curves are separateted during observational period they will stay that separated during extrapolation period. Obviously these conclusions are based on simplification & generalization. 

<img width="497" height="732" alt="image" src="https://github.com/user-attachments/assets/ecde3a57-8c46-4912-921d-93e73f23899e" />

You may also study the raw hazards, based on reconstructed IPD, and compare them to the hasards implied by the model. 

# Ver 2.0 add some genAI
The end results of this app can be consired as building blocks of PSM. Progression Free Survival (PFS) curves could be handled as OS curves.
1. GenAI to extract dosing regimen information from SmPCs?
2. GenAI to extract comparative asessment results from Fimea, NICE etc.
genA
