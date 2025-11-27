
---

# 3. Session 1 — Distribution & Copula Analysis

## 3.1 Descriptive Statistics

### Key results（来自项目输出）

| Statistic | BTC | ETH |
|----------|---------|---------|
| Mean | 0.00226 | 0.00372 |
| Variance | 0.00161 | 0.00386 |
| Std | 0.0401 | 0.0621 |
| Skewness | -0.82 | 0.005 |
| Kurtosis | 11.9 | 7.67 |

**结论**：两者均具有显著的肥尾 + 尖峰现象（高峰厚尾）。

BTC–ETH 相关系数：  
**0.5445（显著正相关）**

---

## 3.2 Normality Tests

使用了三种检验：

- Shapiro–Wilk  
- Kolmogorov–Smirnov  
- Jarque–Bera  

两个资产所有 p-value 均为 **0.000** → **强烈拒绝正态性**。

---

## 3.3 Distribution Visualization

每个资产绘制：

- 直方图 + Normal PDF  
- QQ plot  
- ECDF  

图形显示：

- 重尾非常明显  
- 正态分布严重低估尾部概率  

---

## 3.4 Moment Generating Function (MGF)

在 t ∈ [-1,1] 网格上计算：

\[
M_X(t) = E[e^{tX}]
\]

BTC 与 ETH MGF 均呈明显非对称，符合前述偏度 + 高峰效应。

---

## 3.5 Probability Integral Transform (PIT)

构造：

\[
u_t = \hat{F}_{BTC}(btc\_ret), \quad v_t = \hat{F}_{ETH}(eth\_ret)
\]

PIT 散点图显示明显的正相关结构。

---

## 3.6 Copula Model Fitting

对 (u_btc, v_eth) 估计 5 类 Copula：

| Copula | LogLik | AIC | BIC |
|-------|--------|--------|--------|
| **Student t** | 537.41 | **-1070.82** | **-1059.47** |
| Clayton | 461.92 | -921.85 | -916.17 |
| Frank | 426.71 | -851.42 | -845.74 |
| Gaussian | 391.95 | -781.90 | -776.22 |
| Gumbel | 372.89 | -743.77 | -738.10 |

### **Winner: Student t Copula**  
（AIC & BIC 双最优）

**t-Copula 参数：**

- ρ ≈ 0.605  
- ν ≈ 2.60（非常重尾）

→ ETH 与 BTC 的依赖结构存在 **强正相关 + 联合下跌肥尾风险（tail dependence）**。

---

# 4. Session 2 — Regression Analysis

## 4.1 Model 1: ETH ~ BTC

核心结果：

\[
ETH_t = 0.0018 + 0.8431 \cdot BTC_t
\]

- β = **0.8431**（高度显著）  
- R² = **0.296**  

说明：BTC 对 ETH 具有强的线性解释能力。

---

## 4.2 Model 2: ETH ~ BTC + log(Volume)

结果显示 Volume 系数不显著：

- β₂(vol) = -0.00048，p = 0.273  
- R² ≈ 0.297（几乎无提升）

→ 交易量对 ETH 日收益率 **并无显著额外解释力**。

---

## 4.3 Model 3 (Optional): Nonlinear ETH ~ BTC²

加入平方项后：

- R² 上升到 **0.306**  
- BTC² 强显著  

ETH 存在轻微的非线性关系。

---

# 5. Session 3 — Time-Series Models

## 5.1 AR / MA / ARMA Comparison

BTC 最优：**AR(1)**  
ETH 最优：**ARMA(1,1)**  

但整体 AIC 差距不大。

---

## 5.2 GARCH Volatility Modeling

对 BTC / ETH 估计：

- ARCH(1)
- GARCH(1,1)

两者均显示：

- 强烈的 **波动聚集现象**  
- GARCH(1,1) AIC 显著更低 → **最佳模型**

---

## 5.3 GARCH(1,1) VaR Backtest (ETH)

1-step VaR(95%)：

- 杠杆 t 分布  
- 拟合质量良好  
- 违约率（breach rate）：**1.7%**（略低于理论 5% → 模型偏保守）

---

# 6. VAR Model (BTC & ETH)

选择滞后阶 p = 1。

VAR(1) 系数：

- ETH 受到过去 BTC 的显著影响  
- 两资产残差相关：**0.547**

### Forecast Comparison (RMSE/MAE)

| Asset | Model | RMSE | MAE |
|--------|--------|-----------|-----------|
| BTC | VAR | **0.0400** | **0.0257** |
| BTC | AR | 0.0415 | 0.0267 |
| ETH | AR | **0.0612** | **0.0406** |
| ETH | VAR | 0.0620 | 0.0412 |

### 结论：
- **BTC:** VAR > AR  
- **ETH:** AR > VAR  
BTC 对 ETH 的影响较强，但 ETH 动态更多来自自身噪声。

---

# 7. Final: t-Copula + GARCH Joint Predictive Simulation

## 7.1 GARCH Marginal Forecasts

示例：

BTC: mu = 0.14%, sigma = 4.14%
ETH: mu = -0.21%, sigma = 5.96%


## 7.2 t-Copula Joint Simulation (10,000 draws)

使用估计的：

- ρ = 0.5466  
- ν = 5（copula DOF）

模拟 next-day 联合收益率。

## 7.3 Risk Metrics

| Metric | Value |
|--------|--------------|
| BTC 95% VaR | **-9.847%** |
| ETH 95% VaR | **-13.876%** |
| Portfolio 95% VaR | **-10.432%** |
| P(BTC<-5% & ETH<-5%) | **9.13%** |

**联合暴跌概率比各自暴跌概率明显更高**  
→ 重尾 + tail dependence 的典型特征（正如 Copula 分析所示）。

---

## 7.4 Joint Density Visualization

绘制：

- 2D KDE + Contour  
- Zoomed 突出分布核心区域  
- 两资产的边际预测密度

图形显示：

- 分布呈拉长椭圆 → 明显正相关  
- 重尾 → 分布边缘扩散  
- ETH 波动显著大于 BTC  

---

# 8. Conclusions

本项目呈现了 BTC 与 ETH 市场的若干重要特征：

### ✔ 两资产收益率 **强烈非正态（高峰厚尾）**  
### ✔ 显著的 **正相关与尾部依赖 → 风险联动性强**  
### ✔ t-Copula 是明显优胜者（最适合建模两资产依赖结构）  
### ✔ 回归显示 BTC 是 ETH 的核心驱动因素  
### ✔ 时间序列模型：ETH = ARMA(1,1)，BTC = AR(1)  
### ✔ GARCH(1,1) 捕捉到了真实波动聚集  
### ✔ t-Copula + GARCH 联合预测提供：  
- 更真实的联合尾部风险  
- 更可靠的 VaR 估计  
- 能模拟罕见的市场共振暴跌事件  

---

# 9. Deliverables Summary

本 README 已包含：

- Section A–D 全部步骤  
- 全部实验方法  
- 关键统计结果  
- 关键图形说明  
- 风险指标（VaR、联合暴跌概率）  
- 预测模型比较  

可直接作为项目报告写作基础。

