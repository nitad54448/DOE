# General Purpose Experiment Designer & Analyzer
### Manual for v1.2.0

This document is a guide to the **General Purpose Experiment Designer & Analyzer** tool and it is split into two parts:

* **Part I: The User's Guide** will show how to use the tool, what the different options mean for your experiments, and how to interpret the results.

* **Part II: The Technical Reference** is for understanding the working of the program, if you want to modify some routines. This section contains the specific function names, mathematical equations, and detailed statistical formulas for the analysis.

---
---

# Part I: The User's Guide
## Getting Started and Interpreting Results


### **1. The Workflow**

The tool is designed to follow the natural workflow of a scientific experiment.

1.  **Step 1: Define Your Experiment**
    This is where you define the entire setup on a single screen.
    * **Factors**: What can you control? Define your factor names (e.g., Temperature, Pressure) and the safe operating range for each (their minimum and maximum values). Up yo 6 factors can be controlled, be advides that the number of experiments are typically 2^k.
    * **Responses**: What will you measure? Decide how many responses you need (1 to 3) and give them descriptive names (e.g., Yield, Purity, Cost). Optimization of each "response" (like yield, purity, etc) or a linear combination of them is possible.
    * **Design**: Choose an experimental "type" or design that best fits your goals. Several most common experimental desings are included. For some other methods (e.g. Bayesian, ..) you need to modify the program or use other specialized packages.

2.  **Step 2: Generate the Experimental Plan**
    After setup, the tool will generate an experimental plan to follow. This isn't a random set of experiments; it's a scientifically chosen set of runs designed to give you the most information about your process with the least amount of work. For best results, the runs must be reproducible. If some parameters suggested by the program are not always achieved or achievable (e.g., the program suggests 1000°C but you measured at 980°C), you can edit the value in the experimental table. It may not be optimal as a design point, but it will be correctly used in the calculations.

3.  **Step 3: Run Experiments and Enter Data**
    Perform the experiments as laid out in the plan. Afterwards, return to the tool and enter your measured results into the data table. If you just want to try the tool, you can use the **"Fill with Test Data"** button to see how it works.

4.  **Step 4: Analyze, Optimize, and Understand**
    The program analyzes your results and builds a mathematical model of your process as you fill the data table. It tells you how good that model is, which factors are most important and can hopefully predict the best settings to achieve your goals. It also helps you find the best compromise when you are optimizing for multiple goals.

5.  **Step 5: Visualize and Export Data** 
    Finally, you can explore your results with 2D contour plots to "see" how the factors affect the outcome. When you're ready, you can export a PDF report of the entire analysis.

### **2. Setting Up Your Experiment**

A successful analysis begins with a well-defined experiment.

#### **2.1. Defining Your Process Factors & Responses**

In the first section of the tool, you define the parameters you can control and measure.

* **Factors**:
    * **Factor (Unit)**: Give your factor descriptive names (e.g., "Temperature (°C)").
    * **Min / Max**: Set the lowest and highest values you want to test for that factor. This defines your experimental range; the values can be in units of your choice.
    * **Varying vs. Fixed Factors**: If you set the Min and Max values to be the same, the tool will treat that factor as a fixed constant and will not include it in the optimization. For a factor to be studied, its Min and Max values must be different.
* **Responses**:
    * **Number of Responses**: Select how many different outcomes you plan to measure (max 3).
    * **Response Name**: Give each response a clear name (e.g., "Yield (%)", "Purity", ...").

#### **2.2. Choosing the Right Experimental Design**

The tool offers several "methods" or designs, grouped by their purpose. Your choice depends on your goal.

##### **Screening Designs**
*Use these when you have many factors and want to quickly identify which ones are most important.*

* **Full Factorial Design**:
    * The most thorough screening design. Use it when you have a few factors (2-4) and want to understand all main effects and their interactions.
* **Fractional Factorial Design**:
    * The workhorse for efficient screening (3-7 factors). It uses a fraction of the runs of a Full Factorial, saving time and resources. It's excellent for identifying the most important factors, assuming complex interactions are less important.
* **Taguchi (L8) Design**:
    * An efficient screening design for identifying the strongest main effects in only 8 experiments. Use this when you need a very fast and economical screen and can assume interactions are negligible.

##### **Optimization Designs**
*Use these after screening, when you know the key factors and want to find their optimal settings.*

* **Box-Behnken Design (BBD)**:
    * An efficient design for optimization (requires at least 3 factors). It's a good choice if you need to avoid running experiments at extreme conditions (e.g., where both temperature and pressure are at their absolute maximum).
* **Central Composite Design (CCD)**:
    * The most popular design for optimization. It gives a complete picture of your process, including any curvature in the response, allowing you to reliably find the "peak" of your performance.
    * **CCD Type Option (Face-Centered vs. Rotatable)**: This option fine-tunes the design.
        * **Face-Centered**: Choose this if you absolutely must stay within your defined Min/Max limits for all experiments.
        * **Rotatable**: This is the default and usually the best choice. It gives you the most consistent prediction quality across your entire process space. *Note: This may suggest a few experimental runs that are slightly outside your initial Min/Max values.*

### **3. Understanding the Results**

After you enter your data, the tool presents several statistical parameters. Here's how to make sense of them.

#### **3.1. The Model's Report Card: Key Statistics Explained**

* **R-Squared (R²)**: An overall score (from 0 to 1) for how well the model fits your data. A value of 0.95 means the model explains 95% of the behavior you observed. Higher is better.
* **Adjusted R-Squared (Adj R²)**: The "honest" R-squared. It's a more reliable score because it accounts for how complex your model is. This is usually the best indicator of a good fit.
* **Predicted R-Squared (Pred R²)**: This tells you how well the model is likely to predict future experiments. If this value is close to your Adjusted R², you can be confident your model is robust.
* **Adequate Precision**: A critical check. It measures the model's signal against its background noise. **You need this value to be greater than 4**. If it's not, your model is too noisy to be used for navigating to an optimum.


#### **3.2. The Levers of Your Process: Interpreting Model Terms**

The model gives you a table of "coefficients" for each term. These numbers tell you how the "levers" of your process work.

* **Linear Terms (e.g., `Pressure`)**: This is the main effect. A positive number means increasing pressure increases your response. A negative number means it decreases it. The larger the absolute value, the stronger the effect.
* **Interaction Terms (e.g., `Pressure*Temperature`)**: *[Note: These only appear in optimization designs]*. This tells you if factors work together or against each other.
* **Quadratic Terms (e.g., `Pressure*Pressure`)**: *[Note: These only appear in optimization designs]*. This is the most important term for optimization because it shows curvature.
    * A **NEGATIVE** value is **GOOD** for maximization! It means the process has a "peak" or a "sweet spot."
    * A **POSITIVE** value indicates a "valley" or a trough, meaning the best results are at the extremes of your range.

#### **3.3. Finding the Best Settings: Optimization**

* **Single Goal**: For each response, the tool will show you the predicted best settings to get the highest possible value.
* **Competing Goals (The Importance Sliders)**: What if the best settings for Response 1 are not that good for Response 2? The sliders let you define your priorities. By setting them (e.g., 70% importance for Response 1, 30% for Response 2), you ask the tool to find the single best **compromise**.

#### **3.4. What is the "Weighted Score Model"?**

When you use the importance sliders, a "Weighted Score Model" appears. This isn't a model of any single response. It's a model of **your preference**. It's built on a "Weighted Score" that combines your actual experimental results according to your slider settings. The statistics for this model tell you how predictable your desired **compromise** is.

### **4. Visualizing Your Process: Contour Plots**

The 2D plots are your maps to the optimum. *[Note: Plots are most meaningful for optimization designs that model curvature].*
* **Reading the Map**: The colors show your response value (e.g., red is high, blue is low). The axes are two of your factors.
* **The Star (★)**: The star shows the exact predicted location of the maximum value for that response.
* **Other Factors**: If you have more than two factors, the plots are drawn by holding the other factors steady at their middle value to create a clear 2D slice.

### **5. Exporting Your Report**

When you're finished, you can click the "Save Summary as PDF" button to get a report with all your data, models, and optimal values. The filename is timestamped.

---
---

# Part II: The Technical Reference
## Algorithms and Equations

This section contains mathematical and algorithmic details of the tool.

### **6. Design of Experiments: Implementation Details**

The designs are generated by specific functions within the tool's code.

* **Full Factorial Design**: Implemented by `generateFactorial`. Creates a design matrix for *k* factors with $2^k$ runs.
* **Fractional Factorial Design**: Implemented by `generateFractionalFactorial`. Creates a Resolution III (for k=3) or IV (for k=4-7) design in 8 or 16 runs.
* **Taguchi (L8) Design**: Implemented by `generateTaguchiL8`. Creates an 8-run orthogonal array to study up to 7 main effects.
* **Box-Behnken Design (BBD)**: Implemented by `generateBoxBehnken`. Requires a minimum of 3 factors.
* **Central Composite Design (CCD)**: Implemented by `generateCentralComposite`. The `α` value is set by the user's choice of "Face-Centered" ($\alpha=1$) or "Rotatable" ($\alpha = k^{1/4}$).

### **7. Mathematics and Statistics**

#### **7.1. Variable Coding and Normalization**
Factor levels are normalized to a dimensionless [-1, 1] range prior to fitting and converted back after that.

* The `codeValue` function applies the transformation:
    $$\text{Coded Value} = \frac{2 \times (\text{Real Value} - \text{Min Value})}{(\text{Max Value} - \text{Min Value})} - 1$$

#### **7.2. The Polynomial Model**
The `buildModel` function constructs and fits a model appropriate for the design type.
* **Screening Designs (Fractional Factorial, Taguchi)**: A main-effects-only linear model is fitted:
    $$y = \beta_0 + \sum_{i=1}^{k} \beta_i X_i + \epsilon$$
* **Optimization Designs (Full Factorial, BBD, CCD)**: A full second-order (quadratic) model is fitted:
    $$y = \beta_0 + \sum_{i=1}^{k} \beta_i X_i + \sum_{i=1}^{k} \beta_{ii} X_i^2 + \sum_{i<j}^{k} \beta_{ij} X_i X_j + \epsilon$$

#### **7.3. Parameter Estimation via Least Squares**
The coefficients ($\beta$) are determined by using Least Squares via the matrix equation:
$$\mathbf{b} = (\mathbf{X}^T \mathbf{X})^{-1} \mathbf{X}^T \mathbf{y}$$

### **8. Detailed Statistical Parameter Definitions**

* $y_i$: Observed response.
* $\hat{y}_i$: Model-predicted response.
* $\bar{y}$: Mean of observed responses.
* $n$: Number of valid runs.
* $p$: Number of model terms.

* **Total Sum of Squares ($SS_{Total}$)**: $\sum_{i=1}^{n} (y_i - \bar{y})^2$
* **Sum of Squared Errors ($SS_{Error}$)**: $\sum_{i=1}^{n} (y_i - \hat{y}_i)^2$
* **R-Squared (R²)**: $1 - (SS_{Error}/SS_{Total})$
* **Adjusted R-Squared (Adj R²)**: $1 - ( (SS_{Error}/(n-p)) / (SS_{Total}/(n-1)) )$
* **PRESS**: $\sum_{i=1}^{n} (y_i - \hat{y}_{i,(-i)})^2$
* **Predicted R-Squared (Pred R²)**: $1 - (PRESS/SS_{Total})$
* **Standard Deviation (S)**: $\sqrt{SS_{Error}/(n-p)}$
* **C.V.%**: $(S / \bar{y}) \times 100$
* **Adequate Precision**: $(\max(\hat{y}) - \min(\hat{y})) / \sqrt{p \cdot S^2 / n}$
* **95% Confidence Interval (CI)**: $\beta_j \pm 1.96 \times SE(\beta_j)$

### **9. Optimization Algorithms**

The optimal conditions are found using the `findOptimumRandomWalk` algorithm which searches the modeled response surface for a maximum. The `findCombinedOptimum` function uses the same algorithm but its objective is to maximize the weighted sum of the individual predicted responses.

### **10. The `weightedResponse` Calculation**

For the "Weighted Score Model" analysis, the `analyzeData` function computes a composite score for each experiment. This score is a normalized, weighted average of the measured responses based on the importance sliders. This `weightedResponse` vector is then passed to the `buildModel` function as the `y` variable.

### **11. About the Tool**
* **Filename**: doe-general-v1.2.html
* **Version**: 1.2.0
* **Date**: July 22, 2025
* **Author**: NitaD, Univ. Paris-Saclay
