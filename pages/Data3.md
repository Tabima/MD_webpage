---
title: Data3
---

#' # Data Analysis 3. 
#' ## Javier F. Tabima
#' ### TA: Ben. 
#' ### Lab hours: M 12-1
#+ echo=FALSE, warnings=FALSE, fig.align="center", messages=FALSE
options(warn=-1)
#+ echo=FALSE, warnings=FALSE, fig.align="center", messages=FALSE
library(openintro)
library(ggplot2)
library(grid)
source(url("http://stat511.cwick.co.nz/code/stat_qqline.r"))
source("~/Documents/Scripts/multiplot.R")

#' # Introduction
#'
#' We want to determine if there is a relationship between the weight and the height of individuals. We used a dataset that contains body girth measurements and skeletal diameter measurements, as well as age, weight, height and gender, for 507 individuals - 247 men and 260 women. We are interested in determining if the weight of the individuals can be explained or predicted by height, and to see if this prediction is positively related (weight increases with height) or negatively related (weight decreases with height).
#'
#' # Methods
#' We decided to measure the relationship between both variables using linear regressions. Using the aforementioned dataset, we compared the height and the weight the 507 individuals. First of all, we decided to check for any violation of the assumptions for the lineal regression method (Figure 1). We checked for normality using normality plots (Figure 1a), linearity and constant variance using residuals-fitted value plots (Figure 1b) and residuals-explanatory variable plots (Figure 1c). All the plots show that there are no violations of the assumptions, so we decided to use the lineal regression approach.


#' Residuals against explanatory variable

#+ echo=FALSE, warnings=FALSE, fig.align="center", messages=FALSE
fit <- lm(wgt ~ hgt, data = bdims)
p1 <- qplot(sample = .resid, data = fit, main="Figure 1a. Normal probability plot" ) + stat_qqline()
p2 <- qplot(.fitted, .resid, data = fit, main="Figure 1b. residuals against fitted values plot") + geom_hline(yintercept = 0) + geom_smooth()
p3 <- qplot(hgt, .resid, data = fit, main="Figure 1c. Residuals against explanatory plot", xlab="Height (cm)") + geom_hline(yintercept = 0) +  geom_smooth()
#+ echo=FALSE, warnings=FALSE, fig.align="center", messages=FALSE
multiplot(p1,p3,p2, cols=2)

#' # Summary
#' After performing the analysis, we estimate the mean weight of a person of 0 cm to be -105 kg under this lineal regression model: **μ{Weight|Height}=β0 + β1Height** (Figure 2). We also estimate that an increase in height of 1 cm is associated with an increase in mean weight by 1.01762 kg. Also, with 95% confidence, the mean weight of person of 0 cm of height is between -119.824 and -90.20 kg, and with 95% confidence, an increase in weight of 1 cm is associated with an increase in mean weight between 0.931 and 1.104 kg.
#'
#+ echo=FALSE, warnings=FALSE, fig.align="center", messages=FALSE
qplot(hgt, wgt, data = bdims, main="Figure 2. Lineal regression", xlab="Height (cm)", ylab="Weight (kg)") +  geom_smooth(method = "lm")
#' We wanted to estimate the mean weight of a person that is 183 cm tall. We performed the prediction and we estimated that the mean weight of a person that is 183 cm tall is 81.22 kg, and with 95% confidence, the mean mean weight of a person that is 183 cm tall is between 62.88 and 99.55 kg. Still, this prediction is not very accurate because we are using the predicted interval values(red line,Figure 3), which are much more spread that the mean confidence intervals for that particular point (Confidence intervals for the mean at 183 cm are between 79.90 cm and 82.52 cm, grey bar in Figure 3).
#+ echo=FALSE, warnings=FALSE, fig.align="center", messages=FALSE
newdf <- data.frame(hgt = c(183))
PI <- cbind(newdf, predict(fit, newdf, interval = "prediction"))
qplot(hgt, wgt, data = bdims, main="Figure 3. Lineal regression plot with emphasis on the predicted values",) + geom_pointrange(aes(y = fit, ymin = lwr, ymax = upr), colour = I("red"), data = PI)  +  geom_smooth(method = "lm")
