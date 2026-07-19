# Ver 1.0
What I had in my mind was OS curves of typical oncology drug in a non-curative setting (i.e. metastatic disease). These together with PFS curves, utilities and dosing & cost information are often used to inform Partitioned Survival health economic Model (PSM). The first step is to focus on modelling gained life years, which is arguably the most important driver of the end result in the PSM. Mechanistic cost calculation does not provide such artistic satisfaction, which is needed during the summer holiday.

While the app is created by the genAI tools of one of the tech giants the app itself is deterministic. These tools are proprietary and I have made the purchase by myself. In practise the code is R using Shiny. 

### Step 1: get a Kaplan-Maier figure of overal survival OS & load the figure into app
preferably a good quality, high resolution figure of decent size.
You may download that in jpeg, png etc. The app allows comparison of two curves from the same figure. This is to say comparison is just to the control group of the same trial and no indirect comparisons are possible at the moment. 

The sample picture is automaticlly generated. Here are some other random alternatives:
EX1
EX2

<img width="396" height="462" alt="image" src="https://github.com/user-attachments/assets/98c5c82e-2538-4975-a69d-3b44eb94c2d0" />

 
### Step 2: calibrate the figure
if automatic calibration fails do it manually by setting origin, X-max and Y-max by mouse.

<img width="1602" height="666" alt="image" src="https://github.com/user-attachments/assets/bb328e04-7916-45a4-bf33-f4e0e069b6e7" />

### Step 3: Trace the curves
use atomatic curve tracing or do it manually. Trace treatment and control curves by giving the app some point on the curve using mouse. Autotrace implements a property of K-M curves: when x increases y is non-increasing i.e. monotonicity property. Retrace if you are not satisfied with the result. Often several attempts are needed.

<img width="1627" height="742" alt="image" src="https://github.com/user-attachments/assets/92f1982b-25da-4096-bc2b-64abef881ae0" />


### Step 4: reconstruct IPD
From the previous traced K-M curves indivial patient data are recreated. This data is then used for modelling.

<img width="360" height="85" alt="image" src="https://github.com/user-attachments/assets/322ba6a2-6f08-4142-ae23-575ad5d6351b" />

### Step 5: fit standard parametric models
Standard parametric models are fitted to the IPD and extrapolated. You may set the extrapolation horizon. The default is 120 and the assumption is that the time units are in months (i.e. 120 corresponds to 10 years). AIC and BIC values are shown to describe the statistical fit of each of models. No flexible spline or other more complex models are avaibable thus far.
You may implement tail trimming when fitting the models. The effect of tail trimming can be evaluated in the next sheet.

<img width="1775" height="755" alt="image" src="https://github.com/user-attachments/assets/ce3fb795-c06d-4cc0-b3fa-2e36cbd2b947" />

### Step 6: choose the best model for inspection - combined model comparison
Choose one of the models (for the treatment and for the control, does not need to be the same) for more detailed inspection.

<img width="971" height="742" alt="image" src="https://github.com/user-attachments/assets/f402dba8-21a7-4c7f-b9f1-8a261480b207" />

Now you can see some statistics calculated. How many life years are gained over the extrapolation horizon in AUC interpretation (both undiscounted and discounted). This is the Restricted Mean Survival Time RMST: 
```math
RMST(\tau)=\int_0^\tau S(t)\,dt
```

where the t refers to extrapolation horizon length. Also the difference in modelled medians is shown.
Public opinion is often that just a few months can be gained with the new treatment. With IO treatments this statement is somewhat imprecice. Many patients or even majority of them have negligble benefit, but some share 20-40 % can have very long lasting effect. So one can either have significant benefit or almost none at all. 

<img width="497" height="732" alt="image" src="https://github.com/user-attachments/assets/ecde3a57-8c46-4912-921d-93e73f23899e" />


# Ver 2.0 add some genAI
