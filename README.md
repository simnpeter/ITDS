# ITDS
This repo is for my final project, in the following I will write down an explanation for it.

## Exercise 1
- Firstly I imported all the necessary packages for this project  
- Defined the functions for data generation  
- Made helper functions for model evaluation, plotting, dataset creation etc.  
- I found that since I have to run the whole pipeline for each function, the easiest was to put everything inside a `for` loop. Not the cleanest, but it works.

### 1.1 Create dataset
I created the dataset using the code snippet from the PDF.

### 1.2 Draw and inspect the function
I plotted the functions to visually inspect their shape. This helped me understand which models might perform better.  
For example:
- `f1` is relatively smooth and linear-ish → Linear Regression might do okay
- `f2` and `f3` are nonlinear → SVR or Random Forest might be better choices

### 1.3 Frame a regression task
I started with **Linear Regression** as a baseline. Then I tested:
- **Ridge Regression**
- **SVR (Support Vector Regressor)**
- **Random Forest Regressor**
- **MLPRegressor (Neural Network)**

I evaluated them using:
- Mean Squared Error (MSE)
- R² Score

Each model was trained on the training set and predictions were made on the test set.

### 1.4 Evaluate results
The models performed differently depending on the function:
- Linear Regression was good only for `f1`
- SVR and MLP did better on `f2`, `f3`
- Random Forest was consistently strong, especially with noise

### 1.5 Feature engineering
I added polynomial features (like `x²`) to the input data and re-trained the best-performing models.  
This helped improve the accuracy, especially on `f2` and `f3`.

### 1.6 Noise robustness
I added Gaussian noise to the targets using a custom `inject_noise()` function. Then I re-ran the models:
- Linear Regression struggled the most
- Random Forest and MLP were the most noise-resistant

---

## Exercise 2
This part was about **multivariate regression** using synthetic data from `make_regression`.

### 2.1 Generate synthetic data
I used the code snippet from the PDF.
- I then split the data and trained multiple regressors.

### 2.2 Test regression pipeline
I reused the pipeline from Exercise 1, but updated it to support multiple input features.
Models adapted well. Performance improved when informative features were clearly separated.

### 2.3 Harder dataset
I increased the noise level and reduced the number of informative features. This made it harder for the models to generalize.
- I also looked at the learned coefficients in Linear Regression.
- Non-informative features had weights close to zero → confirming model behaved correctly.

---

## Exercise 3
This part was about time series forecasting using real temperature data from World War II.

### 3.1 Load dataset
I loaded the SummaryOfWeather.csv file.

### 3.2 Preprocessing
- Converted Date to datetime64
- Removed missing values
- Extracted and plotted the MeanTemp time series

### 3.3 Filter the data for sensor 22508
I filtered the dataset to keep only the measurements from sensor `22508` (located in Honolulu).  
I sorted the data by date and checked for missing values in the `MeanTemp` column.  
This sensor had data from 1940 to 1945, with some missing temperature values.

### 3.4 Plot the temperature series
I plotted the complete time series of daily mean temperatures for sensor 22508.  
Then I handled the missing values using **linear interpolation**.  
If any were still missing, they were filled with the column mean.

### 3.5 Create a rolling window
To structure the time series for forecasting, I created a rolling window dataset.  
For a given `window_size` (default 7), each sample consisted of 7 consecutive days of temperatures, and the label was the temperature on day `t + 8`.  
This transformed the time series into a supervised learning format.

### 3.6 Train/test split
I split the data based on time:
- Training set: data from 1940 to 1944
- Test set: data from 1945

I applied the same rolling window indexing to separate train/test samples accordingly.

### 3.7 Train and evaluate models
I tested five models:
- Linear Regression
- Ridge Regression
- Random Forest
- SVR (RBF kernel)
- MLP Regressor (Neural Network)

Each model was trained on the training data and evaluated on the 1945 test set using:
- **Mean Squared Error (MSE)**
- **R² Score**

I selected the model with the lowest MSE as the best one.  
This model was used in the next steps for visualization and tuning.


### 3.8 Visualization of predictions
I plotted the actual vs predicted temperatures for 1945 using the best performing model.  
The curves are similar, showing that the model captured the seasonal trend quite well.  
However, exact day-to-day values had some noise.  
Forecasting longer horizons (more than 1 day) isn’t ideal with this approach, since each prediction only depends on the previous fixed-length window — not ideal for capturing long-term dependencies.

### 3.9 Hyperparameter tuning – window size
I tested different rolling window sizes: `[3, 7, 14, 30]`  
For each, I:
- Generated training/testing sets
- Re-trained the best model
- Evaluated MSE and R² score

Results varied significantly:
- Smaller windows struggled with trend
- Larger windows captured seasonality better but risked overfitting

**Optimal window size** was selected based on lowest MSE.  
Final forecast vs actual was plotted again for the best window.
