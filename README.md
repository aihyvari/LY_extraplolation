# Ver 1.0
What I had in my mind was OS curves of typical oncology drug in a non-curative setting (i.e. metastatic disease). These together with PFS curves, utilities and dosing & cost information are often used to inform Partitioned Survival Health Economic Model.

### Step 1: get a Kaplan-Maier figure of overal survival  & load the figure into app
preferably a good quality, high resolution figure of decent size.
You may download that in jpeg, png etc.

<img width="396" height="462" alt="image" src="https://github.com/user-attachments/assets/98c5c82e-2538-4975-a69d-3b44eb94c2d0" />

 
### Step 2: calibrate the figure
if automatic calibration fails do it manually by setting origin, X-max and Y-max

<img width="1602" height="666" alt="image" src="https://github.com/user-attachments/assets/bb328e04-7916-45a4-bf33-f4e0e069b6e7" />

### Step 3: Trace the curves
use atomatic tracing or do it manually. Trace treatment and control curves by giving the app some point on the curve using mouse. Autotrace implements a property of K-M curves: when x increases y is non-increasing i.e. monotonicity property. Retrace if you are not satisfied with the result.

<img width="1627" height="742" alt="image" src="https://github.com/user-attachments/assets/92f1982b-25da-4096-bc2b-64abef881ae0" />


### Step 5: reconstruct IPD

<img width="360" height="85" alt="image" src="https://github.com/user-attachments/assets/322ba6a2-6f08-4142-ae23-575ad5d6351b" />

### Step 6: fit standard parametric models
You may set the extrapolation horizon. The default is 120 and the assumption is that the time units are in months. AIC and BIC values are shown to describe the statistical fit.

<img width="1775" height="755" alt="image" src="https://github.com/user-attachments/assets/ce3fb795-c06d-4cc0-b3fa-2e36cbd2b947" />

### Step 7: choose the best model for inspection

<img width="971" height="742" alt="image" src="https://github.com/user-attachments/assets/f402dba8-21a7-4c7f-b9f1-8a261480b207" />


# Ver 2.0 add some genAI
