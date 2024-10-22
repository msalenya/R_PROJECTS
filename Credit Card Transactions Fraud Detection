#PERFORMING AN EXPLORATORY DATA ANALYSIS ON A FRAUD DATASET USING DPLYR
#Data Manipulation with DPLYR
library(dplyr)
library(ggplot2)
library(plyr)
library(stringr)
library(purrr)
library(eeptools)
library(animation)


#Importing Data
setwd("C:/Users/Michael/Desktop/Credit Card Transactions Fraud Detection Dataset")

fraudtrain <- read.csv("fraudTrain.csv")
fraudtest <- read.csv("fraudTest.csv")

fraud <- rbind(fraudtrain , fraudtest)
View(fraud)
fraud <- data.frame(fraud) 

#Structure of the data set
str(fraud)
head(fraud)
summary(fraud)

#Checking for missing values
fraud_clean <- is.na(fraud)
sum(fraud_clean)
dim(fraud_clean)


#Checking for duplicate values
fraud_duplicate <- fraud_clean[duplicated(fraud_clean), ]
sum(fraud_duplicate)
fraud_duplicate

# Exploring the data
glimpse(fraud_unique)
View(fraud_unique)

# Separating the trans_date_trans_time
fraud[c("trans_date" , "trans_time")] = str_split_fixed(fraud$trans_date_trans_time, " ", 2)
View(fraud)

# converting trans_date to date format
fraud$trans_date <- as.Date(fraud$trans_date) 

# Calculating the Age
fraud$dob <- as.Date(fraud$dob)
date_today <- Sys.Date()
fraud$age <- floor(age_calc(as.Date(fraud$dob, date_today), units = "years"))
View(fraud)

# Converting Categorical features to factors  
fraud$merchant <- as.factor(fraud$merchant)
fraud$category <- as.factor(fraud$category)
fraud$first <- as.factor(fraud$first)
fraud$last <- as.factor(fraud$last)
fraud$gender <- as.factor(fraud$gender)
fraud$street <- as.factor(fraud$street)
fraud$city <- as.factor(fraud$city)
fraud$state <- as.factor(fraud$state)
fraud$job <- as.factor(fraud$job)
fraud$trans_time <- as.factor(fraud$trans_time)



## Visualizing the Data


# Most Defrauded Gender
ggplot(fraud[fraud$is_fraud == 1, ], aes(x = gender, fill = gender)) +
  geom_bar() +
  labs(title = "Most Fraudulent Gender",
       x = "Gender",
       y = "Count") +
  theme_minimal()


# Merchant with the Most Transactions
top_merchants <- table(fraud$merchant) %>%
  sort(decreasing = TRUE) %>%
  head(10)
top_merchants

ggplot(data.frame(merchant = names(top_merchants), transactions = as.numeric(top_merchants)), aes(x = reorder(merchant, -transactions), y = transactions)) +
  geom_bar(stat = "identity", fill = "skyblue") +
  labs(title = "Top 10 Merchants by Total Transactions",
       x = "Merchant",
       y = "Total Transactions") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))



#Merchant with the Most Fraudulent Transactions
top_fraud_merchants <- table(fraud[fraud$is_fraud == 1, ]$merchant) %>%
  sort(decreasing = TRUE) %>%
  head(5)
top_fraud_merchants


ggplot(data.frame(merchant = names(top_fraud_merchants), transactions = as.numeric(top_fraud_merchants)), aes(x = reorder(merchant, -transactions), y = transactions)) +
  geom_bar(stat = "identity", fill = "skyblue") +
  labs(title = "Top 5 Fraudulent Merchants by Total Transactions",
       x = "Merchant",
       y = "Total Transactions") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))


#Top 10 States by Total Fraudulent Transactions
top_fraud_states <- table(fraud[fraud$is_fraud == 1, ]$state) %>%
  sort(decreasing = TRUE) %>%
  head(10)
top_fraud_states

ggplot(data.frame(state = names(top_fraud_states), transactions = as.numeric(top_fraud_states)), aes(x = reorder(state, -transactions), y = transactions)) +
  geom_bar(stat = "identity", fill = "skyblue") +
  labs(title = "Top 10 States by Total Fraudulent Transactions",
       x = "State",
       y = "Total Transactions") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))


# Top 5 Individuals by Total Fraudulent Amount
top_fraud_individuals <- aggregate(fraud[fraud$is_fraud == 1, ]$amt, by = list(paste(fraud[fraud$is_fraud == 1, ]$first, fraud[fraud$is_fraud == 1, ]$last)), sum) %>%
  arrange(desc(x)) %>%
  head(5)
top_fraud_individuals

ggplot(top_fraud_individuals, aes(x = reorder(Group.1, -x), y = x)) +
  geom_bar(stat = "identity", fill = "skyblue") +
  labs(title = "Top 5 Individuals by Total Fraudulent Amount",
       x = "Individual",
       y = "Total Amount") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))



#Peak Hours for Fraud Transactions
fraud_data <- subset(fraud, is_fraud == 1)
fraud_data$trans_time <- as.POSIXct(fraud_data$trans_time, format = "%H:%M:%S")

ggplot(fraud_data, aes(x = trans_time)) +
  geom_histogram(binwidth = 3600) +
  labs(title = "Peak Hours for Fraud Transactions",
       x = "Transaction Time",
       y = "Count") +
  theme_minimal()




# creating a new variable- 0-Male, 1-Female - using ifelse()
fraud$gender <- ifelse(fraud$gender == "F" , 1,0)

#Determining the optimal number of clusters
#Selecting needed features
fraudulent_data <- fraud %>%
  filter(is_fraud == 1)

fraud1 <- fraudulent_data %>%
  select(amt, gender, age)
glimpse(fraud1)


#Checking to see if the data is standard
colMeans(fraud1) #gives you the means across feature
apply(fraud1, 2 , sd) #check for standard deviation across features

#Data Transformation into a standard form
fraud_scale <- scale(fraud1) #transforming data into Standardized form
colMeans(fraud_scale)
summary(fraud_scale)

apply(fraud_scale, 2 ,sd)


# Elbow Plot
set.seed(15)
cluster_point <- map_dbl(1:15, function(k){
  model <- kmeans(fraud_scale, center = k)
  model$tot.withinss
})
cluster_point

elbow_fraud <- data.frame(k = 1:15, cluster_point = cluster_point)
print(elbow_fraud)


#Visualize cluster_point plot
ggplot(elbow_fraud , aes(x= k, y= cluster_point)) +
  geom_line()+
  geom_point()+
  scale_x_continuous(breaks = 1:15)


#Determine 5 clusters using kmean
revscalek <- kmeans.ani(fraud_scale, 5)
revscalek$cluster
View(revscalek$centers)

clusterpoints <- revscalek$cluster
clusterpoints

new_fraud1 <- fraud1 %>%
  mutate(clusterpoints)
View(new_fraud1)




