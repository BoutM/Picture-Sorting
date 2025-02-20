Columbia Classification
================
Maxime Bouthillier
2025-02-12

## Columbia Classification Project - Analysis of Varying Neural Network Models

``` r
# Importing Libraries
library(jpeg)
library(neuralnet)
library(ggplot2)
library(ggthemes)

# Importing the data
pm <- read.csv("/Users/maxb/Documents/SSB/Year 4 Files/Winter Term/MATH 3333/Capstone Project/photoMetaData.csv")
start <- Sys.time()
```

# Creation of Data Partitioning and Summary Statistic Function

``` r
Function <- function(img, f, m, n, Var) {
  
  Xn <- array(NA, c(m,n,3))                   # Reduced Mean RGB
  
  j = 1 
  a = 1
  b = length(img[,1,1])/m                     #Length of Rows / 10
  
  while (j <= m) {                            # Rows
    
    k = 1
    c = 1
    d = length(img[1,,1])/n                   # Length of Columns / 10 
    
    while (k <= n) {                          # Columns 
      
      Xn[j,k,] <- apply(img[a:b,c:d,], 3, f)
      c = c + length(img[1,,1])/m
      d = d + length(img[1,,1])/m
      k = k + 1
      
    }
    a = a + length(img[,1,1])/n
    b = b + length(img[,1,1])/n
    j = j + 1
  }
  if (Var == 1) {
    Xn <- apply(Xn, 3, var)
  }
  else {
  }
  return(c(apply(Xn,2,c)))
}
```

# NN Specifications and Accuracy Data Frame Creation

``` r
# Data Preparation
q <- nrow(pm)
trainFlag <- (runif(q) > 0.3)
y <- as.numeric(pm$category == "outdoor-day")
directory <- "/Users/maxb/Documents/SSB/Year 4 Files/Winter Term/MATH 3333/Capstone Project/Columbia_Images/"

# Defining the m/n matrix specifications as well as Neurons
h <- seq(5,15,1)


# Matrix Array of Accuracy Scores
A_df <- array(NA, dim = c(3, 4, length(h)))


for (x in 1:length(h)) {
  
  #Model Specifications, Hidden Layers and Neuron Specifications
  
  m = h[x]
  n = h[x]
  
  mod.hn <- rbind(c(round(m*n*3*(1/3)), round(m*n*(1/2))),           # First layering style of Neurons 
                  c(round(m*n*3*(2/3))+1, round(m*n*2/3)+1),         # Second layering style of Neurons
                  c(round(m*n*3*(3/2)), round(m*n*3*(3/4))))         # Third layering style of Neurons
  
  
  # Mean Method
  X <- matrix(NA, nrow=q, ncol=3*m*n)
  Accuracy <- matrix(NA, ncol=1, nrow=nrow(mod.hn))
  
  for (i in 1:q) {
    img <- readJPEG(paste0(directory, pm$name[i]))
    X[i,] <- Function(img, "mean", m, n, 0)
  }
  X <- scale(X)

  for (i in 1:length(mod.hn[,1])) {
    nn <- neuralnet(y[trainFlag] ~. , data=X[trainFlag,], hidden=c(mod.hn[i,1], mod.hn[i,2]),
                    linear.output=FALSE, threshold=0.0001)
  
    results <- data.frame(actual = y[!trainFlag], prediction = 
                          round(compute(nn, X[!trainFlag,])$net.result, digits=0))
  
      Accuracy[i] <- sum(results$prediction==results$actual) / 
    (sum(results$prediction==results$actual) + sum(results$prediction!=results$actual))
  }
  
  A_df[,1, x] <- Accuracy

  
  # Median Method
  X <- matrix(NA, nrow=q, ncol=3*m*n)
  Accuracy <- matrix(NA, ncol=1, nrow=nrow(mod.hn))
  
  for (i in 1:q) {
    img <- readJPEG(paste0(directory , pm$name[i]))
    X[i,] <- Function(img, "median", m, n, 0)
  }
  X <- scale(X)

  for (i in 1:length(mod.hn[,1])) {
    nn <- neuralnet(y[trainFlag] ~. , data=X[trainFlag,], hidden=c(mod.hn[i,1], mod.hn[i,2]),
                    linear.output=FALSE, threshold=0.0001)
  
    results <- data.frame(actual = y[!trainFlag], prediction = 
                          round(compute(nn, X[!trainFlag,])$net.result, digits=0))
  
      Accuracy[i] <- sum(results$prediction==results$actual) / 
    (sum(results$prediction==results$actual) + sum(results$prediction!=results$actual))
  }

  A_df[,2, x] <- Accuracy

  
  # Mean Variance Method
  X <- matrix(NA, nrow=q, ncol=3*m*n)
  Accuracy <- matrix(NA, ncol=1, nrow=nrow(mod.hn))
  
  for (i in 1:q) {
    img <- readJPEG(paste0(directory , pm$name[i]))
    X[i,] <- Function(img, "mean", m, n, 1)
  }
  X <- scale(X)

  for (i in 1:length(mod.hn[,1])) {
    nn <- neuralnet(y[trainFlag] ~. , data=X[trainFlag,], hidden=c(mod.hn[i,1], mod.hn[i,2]),
                    linear.output=FALSE, threshold=0.0001)
  
    results <- data.frame(actual = y[!trainFlag], prediction = 
                          round(compute(nn, X[!trainFlag,])$net.result, digits=0))
  
      Accuracy[i] <- sum(results$prediction==results$actual) / 
    (sum(results$prediction==results$actual) + sum(results$prediction!=results$actual))
  }
  
  A_df[,3, x] <- Accuracy

  
  # Median Variance Approach
  X <- matrix(NA, nrow=q, ncol=3*m*n)
  Accuracy <- matrix(NA, ncol=1, nrow=nrow(mod.hn))
  
  for (i in 1:q) {
    img <- readJPEG(paste0(directory, pm$name[i]))
    X[i,] <- Function(img, "median", m, n, 1)
  }
  X <- scale(X)

  for (i in 1:length(mod.hn[,1])) {
    nn <- neuralnet(y[trainFlag] ~. , data=X[trainFlag,], hidden=c(mod.hn[i,1], mod.hn[i,2]),
                    linear.output=FALSE, threshold=0.0001)
  
    results <- data.frame(actual = y[!trainFlag], prediction = 
                          round(compute(nn, X[!trainFlag,])$net.result, digits=0))
  
      Accuracy[i] <- sum(results$prediction==results$actual) / 
    (sum(results$prediction==results$actual) + sum(results$prediction!=results$actual))
  }

  A_df[,4, x] <- Accuracy
}

print(Sys.time() - start)
```

    ## Time difference of 34.72158 mins

``` r
# Mean Method Graph
Mean <- data.frame(data=c(h,h,h))
Mean[,2] <- c(A_df[1,1,], A_df[2,1,], A_df[3,1,])
Mean[,3] <- c(rep("NM-1", length(h)), rep("NM-2", length(h)), rep("NM-3", length(h)))
colnames(Mean) <- c("Partitions", "Accuracy", "Neuron.Method")

ggMean <- ggplot(data=Mean, aes(x=Partitions, y=Accuracy, group=Neuron.Method, color=Neuron.Method)) + 
  theme_hc()+ scale_colour_hc() +
  geom_line()+
  geom_point() + 
  scale_x_continuous(breaks=seq(5,15,1)) +
  scale_y_continuous(breaks=seq(0.30, .90, 0.1), limits=c(0.30, .90)) +
  theme(legend.position="right") + 
  xlab("Partitions (x^2)") +
  ggtitle("Mean Method") 
plot(ggMean)
```

![](Columbia-Classification_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
# Median method Graph
Median <- data.frame(data=c(h,h,h))
Median[,2] <- c(A_df[1,2,], A_df[2,2,], A_df[3,2,])
Median[,3] <- c(rep("NM-1", length(h)), rep("NM-2", length(h)), rep("NM-3", length(h)))
colnames(Median) <- c("Partitions", "Accuracy", "Neuron.Method")

ggMedian <- ggplot(data=Median, aes(x=Partitions, y=Accuracy, group=Neuron.Method,
                                    color=Neuron.Method)) + 
  theme_hc()+ scale_colour_hc() +
  geom_line()+
  geom_point() + 
  scale_x_continuous(breaks=seq(5,15,1)) +
  scale_y_continuous(breaks=seq(0.30, .90, 0.1), limits=c(0.30, .90)) +
  theme(legend.position="right") + 
  xlab("Partitions (x^2)") +
  ggtitle("Median Method")

plot(ggMedian)
```

![](Columbia-Classification_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
# Variance-Mean Method Graph
Var.Mean <- data.frame(data=c(h,h,h))
Var.Mean[,2] <- c(A_df[1,3,], A_df[2,3,], A_df[3,3,])
Var.Mean[,3] <- c(rep("NM-1", length(h)), rep("NM-2", length(h)), rep("NM-3", length(h)))
colnames(Var.Mean) <- c("Partitions", "Accuracy", "Neuron.Method")

ggMean.Var <- ggplot(data=Var.Mean, aes(x=Partitions, y=Accuracy, group=Neuron.Method,
                                    color=Neuron.Method)) + 
  theme_hc()+ scale_colour_hc() +
  geom_line()+
  geom_point() + 
  scale_x_continuous(breaks=seq(5,15,1)) +
  scale_y_continuous(breaks=seq(0.30, .90, 0.1), limits=c(0.30, .90)) +
  theme(legend.position="right") +
  xlab("Partitions (x^2)") +
  ggtitle("Variance-Mean Method")
plot(ggMean.Var)
```

![](Columbia-Classification_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
# Variance-Mean Method Graph
Var.Median <- data.frame(data=c(h,h,h))
Var.Median[,2] <- c(A_df[1,4,], A_df[2,4,], A_df[3,4,])
Var.Median[,3] <- c(rep("NM-1", length(h)), rep("NM-2", length(h)), rep("NM-3", length(h)))
colnames(Var.Median) <- c("Partitions", "Accuracy", "Neuron.Method")

ggMedian.Var <- ggplot(data=Var.Median, aes(x=Partitions, y=Accuracy, group=Neuron.Method,
                                    color=Neuron.Method)) + 
  theme_hc()+ scale_colour_hc() +
  geom_line()+
  geom_point() + 
  scale_x_continuous(breaks=seq(5,15,1)) +
  scale_y_continuous(breaks=seq(0.30, .90, 0.1), limits=c(0.30, .90)) +
  theme(legend.position="right") + 
  xlab("Partitions (x^2)") +
  ggtitle("Variance-Median Method")
plot(ggMedian.Var)
```

![](Columbia-Classification_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
max(A_df[,1,])
```

    ## [1] 0.8215768

``` r
which(A_df[,1,] == max(A_df[,1,]), arr.ind = TRUE)
```

    ##      row col
    ## [1,]   2   8

``` r
max(A_df[,2,])
```

    ## [1] 0.7966805

``` r
which(A_df[,2,] == max(A_df[,2,]), arr.ind = TRUE)
```

    ##      row col
    ## [1,]   2   8

``` r
# Final Model
X <- matrix(NA, nrow=q, ncol=3*15*15)

for (i in 1:q) {
  img <- readJPEG(paste0(directory, pm$name[i]))
  X[i,] <- Function(img, "mean", 15, 15, 0)
}
X <- scale(X)

nn <- neuralnet(y[trainFlag] ~. , data=X[trainFlag,], hidden=c(mod.hn[1,1], mod.hn[1,2]),
                linear.output=FALSE, threshold=0.0001)
  
results <- data.frame(actual = y[!trainFlag], prediction = 
                      round(compute(nn, X[!trainFlag,])$net.result, digits=0))

Accuracy <- sum(results$prediction == results$actual) / (sum(results$prediction == results$actual)) + 
                                                         sum(results$prediction != results$actual)
tp<-sum((results$prediction==1)*(results$actual==1))
tn<-sum((results$prediction==0)*(results$actual==0))
fp<-sum((results$prediction==1)*(results$actual==0))
fn<-sum((results$prediction==0)*(results$actual==1))

misrate <- (fp+fn)/(263)
sensitivity <- tp/(tp+fn)
specificity <- tn/(tn+fp)

roundedresults <- sapply(results, round, digits=0)
roundedresultsdf = data.frame(roundedresults)

table(roundedresultsdf$actual, roundedresultsdf$prediction)
```

    ##    
    ##       0   1
    ##   0 138  21
    ##   1  33  49

``` r
True <- factor(c(0, 0, 1, 1))
Predicted <- factor(c(1, 0, 1, 0))
Y      <- c(fp, tn, tp, fn)
df <- data.frame(True, Predicted, Y)

ggplot(data =  df, mapping = aes(x = True, y = Predicted)) +
  geom_tile(aes(fill = Y), colour = "white") +
  geom_text(aes(label = sprintf("%1.0f", Y)), vjust = 1) +
  scale_fill_gradient(low = "blue", high = "green") +
  theme_bw() + theme(legend.position = "none")
```

![](Columbia-Classification_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->
