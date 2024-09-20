install.packages("prophet")
install.packages("tidyverse")
install.packages("readxl")
install.packages("dplyr")

library(prophet)
library(tidyverse)
library(readr)
library(readxl)
library(dplyr)

df <- read_xlsx("/Users/jio/Desktop/EMRTS intern/enrollment data.xlsx")
head(df)

# I'll focus on data of New Jersey, filter the data
nj_data <- df %>%
  filter(`State Name` == "New Jersey") %>%
  select(`Report Date`, `Total Medicaid and CHIP Enrollment`)
# Convert Report Date to Date type
nj_data$`Report Date` <- as.Date(nj_data$`Report Date`, format="%Y-%m-%d")
nj_data <- nj_data %>%
  rename(ds = `Report Date`, y = `Total Medicaid and CHIP Enrollment`)

model <- prophet()
model <- prophet(daily.seasonality = TRUE)

model <- fit.prophet(model, nj_data)
future <- make_future_dataframe(model, periods = 3650)

forecast <- predict(model, future)

plot(model, forecast)
prophet_plot_components(model, forecast)
tail(forecast[c('ds', 'yhat', 'yhat_lower', 'yhat_upper')],n=3400)  