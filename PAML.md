Machine learning algorithm to predict activity quality
========================

### Data loading


```r
ds <- read.csv("pml-training.csv")
```

### Deleting columns where amount of NAs more than 60%


```r
NAvaluesAllowed <- length(rownames(ds)) * 0.6
cols <- apply(ds, 2, function(x) 
    length(which(!is.na(x))) > NAvaluesAllowed)
    
ds <- ds[,cols]    
```

### Getting rid of variables with near zero variation


```
## Loading required package: lattice
## Loading required package: ggplot2
```

### Remove timestamp, user, num_window column


```r
ds <- ds[, -nzv]
ds <- ds[,-grep("timestamp",colnames(ds))]
ds <- ds[,-1] #remove X column
ds <- ds[,-1] #remove user_name column
ds <- ds[,-1] # remove num_window column
```

### Dividing given dataset on train and test portions


```r
inTrain = createDataPartition(ds$classe, p = 0.6)[[1]]
training = ds[ inTrain,]
testing = ds[-inTrain,]
```

### Building prediction model using random forest method


```r
library(randomForest)
```

```
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
model1 <- randomForest(classe ~ ., data = training, importance=TRUE)
```

### Reading test data and predicting using our model


```r
tds <- read.csv("pml-testing.csv")

colnames <- colnames(training)
tdsa <- tds[,colnames[-53]]
    
ptds <- predict(model1, tdsa)
```
