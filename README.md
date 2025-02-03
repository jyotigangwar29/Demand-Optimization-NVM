# Newsvendor Model Optimization with Nonlinear Programming

## Problem Statement

The **Newsvendor Model (NVM)** is a fundamental framework in operations management used to optimize inventory levels when demand is uncertain. This project extends the standard NVM by incorporating:

1. **Cost Adjustments:** Rush-order costs and disposal fees.
2. **Price-Dependent Demand:** Demand is modeled as a linear function of price.
3. **Nonlinear Programming (NLP):** Reformulating the problem using **quadratic programming (QP)** or **quadratically constrained programming (QCP)**.

By addressing these factors, this model enables dynamic pricing and production adjustments to maximize profitability in a publishing company.

## Mathematical Formulation

### 1. **Traditional Newsvendor Model**
Given:
- $ p $: selling price per unit
- $ c $: production cost per unit
- $ D \sim f(D) $: random demand with known distribution

Objective:
$$
\max_q E[p \min(q, D) - qc]
$$
Using past demand observations $ D_i $, this is approximated as:
$$
\max_q \frac{1}{n} \sum_{i=1}^{n} (p \min(q, D_i) - qc)
$$
This is nonlinear due to $ \min(q, D_i) $, but can be reformulated as a **Linear Program (LP)**:
$$
\max_{q, h_i} \frac{1}{n} \sum_{i=1}^{n} h_i
$$
subject to:
$$
h_i \leq pD_i - qc
$$
$$
h_i \leq pq - qc
$$

### 2. **Extended Model with Rush Orders and Disposal Fees**

Additional Parameters:
- $ g > c $: cost per rushed unit
- $ t \geq 0 $: disposal cost per excess unit

Revised Objective:
$$
\max_q \frac{1}{n} \sum_{i=1}^{n} \left( pD_i - qc - g(D_i - q)^+ - t(q - D_i)^+ \right)
$$
where $ (x)^+ = \max(x, 0) $. Reformulated as an LP by introducing dummy variables.

### 3. **Price-Dependent Demand Model**
Demand is modeled as:
$$
D_i = \beta_0 + \beta_1 p + \epsilon_i
$$
where $ \beta_0, \beta_1 $ are estimated via **Linear Regression**.
Substituting into the profit equation:
$$
\max_{p,q} \frac{1}{n} \sum_{i=1}^{n} (p(\beta_0 + \beta_1 p + \epsilon_i) - qc - g(D_i - q)^+ - t(q - D_i)^+)
$$
which introduces quadratic terms, making it a **Quadratic Program (QP)**.

## Implementation Details

### **Optimization Techniques**
- **Fixed Price Optimization (LP):** Solved using **Gurobi**.
- **Price Optimization (QP/QCP):** Two approaches:
  - **QP Approach:** Keeps quadratic terms in the objective.
  - **QCP Approach:** Moves quadratic terms to constraints.
- **Bootstrapped Sensitivity Analysis:** Generates multiple samples from the dataset to test robustness.

### **Steps in Code Implementation**
1. **Data Preprocessing:**
   - Load historical **price-demand data**.
   - Fit **Linear Regression** to estimate $ \beta_0, \beta_1 $.
2. **Fixed Price LP Solution:**
   - Solve for $ q^* $ using Gurobi.
3. **Price-Dependent QP/QCP Solution:**
   - Introduce **decision variables for price and quantity**.
   - Formulate **QP/QCP** constraints.
   - Solve using Gurobi.
4. **Bootstrapping:**
   - Repeatedly sample data, refit $ \beta_0, \beta_1 $, and solve.
   - Analyze variations in optimal $ p^* $ and $ q^* $.

## Results and Insights

| Model | Optimal Price | Optimal Quantity | Profit |
|--------|--------------|-----------------|--------|
| Linear NVM (Fixed $ p $) | 1.00 | 472 | \$210-220 |
| Nonlinear NVM (QP) | 0.95 | 535 | \$230-240 |

### **Key Findings:**
- Allowing price optimization **increases profit** by ~10%.
- Optimal price $ p^* $ lies between **0.91 - 0.99**.
- Optimal quantity $ q^* $ shifts from **472 to ~535**.
- **Bootstrapping results:** 
  - **Price distribution:** Peaks at **0.95**.
  - **Quantity distribution:** Skewed right, clustering around **500-580**.

### **Visualization:**
- **Histogram of Optimal Price & Quantity** confirms a quadratic demand-price relationship.
- **Profit Distribution Analysis:** Majority falls between **$230-$240**, validating robustness.
- **Scatter Plot of $ p^* $ vs. $ q^* $:** Clear **inverse price-demand trend**.

## Advantages of Nonlinear Programming

1. **More Accurate Demand Modeling:** Captures real-world **price elasticity**.
2. **Better Cost Management:** Incorporates **rush order and disposal fees**.
3. **Higher Profitability:** Optimizes both **price and quantity**.
4. **Flexibility in Inventory Control:** Handles demand uncertainty via **sensitivity analysis**.

## Future Enhancements
- **Incorporate Labor & Logistics Costs:** Improve cost precision.
- **Real-Time Pricing Adjustments:** Implement **dynamic pricing models**.
- **Multi-Stage Optimization:** Account for **multiple production cycles**.

## Conclusion
This project demonstrates how **nonlinear programming** extends the classic Newsvendor Model, improving decision-making through **price elasticity, cost structures, and sensitivity analysis**. By leveraging **quadratic programming and bootstrapping techniques**, the model provides a **more profitable and robust inventory strategy**, making it superior to traditional **linear methods**.
