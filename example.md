Examples with Tensorflow
------------------------

``` r
library(tensorflow)
```

    ## Warning: package 'tensorflow' was built under R version 3.4.4

``` r
# Create 100 phony x, y data points, y = x * 0.1 + 0.3
x_data <- runif(100, min=0, max=1)
y_data <- x_data * 0.1 + 0.3

# Try to find values for W and b that compute y_data = W * x_data + b
# (We know that W should be 0.1 and b 0.3, but TensorFlow will
# figure that out for us.)
W <- tf$Variable(tf$random_uniform(shape(1L), -1.0, 1.0))
b <- tf$Variable(tf$zeros(shape(1L)))
y <- W * x_data + b

# Minimize the mean squared errors.
loss <- tf$reduce_mean((y - y_data) ^ 2)
optimizer <- tf$train$GradientDescentOptimizer(0.5)
train <- optimizer$minimize(loss)

# Launch the graph and initialize the variables.
sess = tf$Session()
sess$run(tf$global_variables_initializer())

# Fit the line (Learns best fit is W: 0.1, b: 0.3)
for (step in 1:201) {
  sess$run(train)
  if (step %% 20 == 0)
    cat(step, "-", sess$run(W), sess$run(b), "\n")
}
```

    ## 20 - -0.01963282 0.3658222 
    ## 40 - 0.05863041 0.3227616 
    ## 60 - 0.08569418 0.3078711 
    ## 80 - 0.09505297 0.3027219 
    ## 100 - 0.09828931 0.3009412 
    ## 120 - 0.09940843 0.3003255 
    ## 140 - 0.09979544 0.3001125 
    ## 160 - 0.09992925 0.3000389 
    ## 180 - 0.09997556 0.3000135 
    ## 200 - 0.09999155 0.3000047

Examples with greta
-------------------

``` r
library(greta)
```

    ## 
    ## Attaching package: 'greta'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     binomial, poisson

    ## The following objects are masked from 'package:base':
    ## 
    ##     %*%, backsolve, beta, colMeans, colSums, diag, forwardsolve,
    ##     gamma, rowMeans, rowSums, sweep

``` r
x <- iris$Petal.Length
y <- iris$Sepal.Length
plot(x,y)
```

![](example_files/figure-markdown_github/greta-1.png)

``` r
int <- normal(0, 5)
coef <- normal(0, 3)
sd <- lognormal(0, 3)

mean <- int + coef * x
distribution(y) <- normal(mean, sd)
m <- model(int, coef, sd)
plot(m)
```

    ## Warning: package 'bindrcpp' was built under R version 3.4.4

``` r
draws <- mcmc(m, n_samples = 1000, chains = 1)
bayesplot::mcmc_trace(draws)
```

![](example_files/figure-markdown_github/greta-2.png)
