library(tidyverse)
library(janitor)
library(lubridate)
library(scales)
## delete data sebelumnnya (optional)
rm(list=ls())
## Cek Data file
dir("Data",full.names=T)

## Get Data source
df1<- read.csv("Data/202201-divvy-tripdata.csv")
df2<- read.csv("Data/202202-divvy-tripdata.csv")
df3<- read.csv("Data/202203-divvy-tripdata.csv")
df4<- read.csv("Data/202204-divvy-tripdata.csv")
df5<- read.csv("Data/202205-divvy-tripdata.csv")
df6<- read.csv("Data/202206-divvy-tripdata.csv")
#df7<- read.csv("Data/202207-divvy-tripdata.csv")
#df8<- read.csv("Data/202208-divvy-tripdata.csv")
#df9<- read.csv("Data/202209-divvy-tripdata.csv")
#df10<- read.csv("Data/202210-divvy-tripdata.csv")
#df11<- read.csv("Data/202211-divvy-tripdata.csv")
#df12<- read.csv("Data/202212-divvy-tripdata.csv")

## Combine data 12 data frame to bike_rides
bike_rides <- rbind(df1,df2,df3,df4,df5,df6)
## Remove empty data 
bike_rides <- janitor::remove_empty(bike_rides,which = c("cols"))
bike_rides <- janitor::remove_empty(bike_rides,which = c("rows"))
## Cek ukuran data frame untk mengetahui apakah ada perubahan
dim(bike_rides)
## change format datetime pada kolom started_at dan endeed_at
bike_rides$started_at <- lubridate::ymd_hms(bike_rides$started_at)
bike_rides$ended_at <- lubridate::ymd_hms(bike_rides$ended_at)
##make new column for time
bike_rides$start_time <- lubridate::hour(bike_rides$started_at)
bike_rides$end_time <- lubridate::hour(bike_rides$ended_at)
## make new columk for date
bike_rides$start_date <- as.Date(bike_rides$started_at)
bike_rides$end_date <- as.Date(bike_rides$ended_at)
## make new column for month
bike_rides$month <- lubridate::month(bike_rides$started_at)
## make plot 
bike_rides %>% count(start_time,sort = T) %>% ggplot() +
  geom_line(aes(x=start_time,y=n))+
  scale_y_continuous(labels = comma) + 
  labs(title ="Garph count of number bike ride by hour(Previous 6 month) ")


### just experiment dont mention it
membership <- bike_rides %>% count(member_casual,short = month)
membership <- rename(membership,status=member_casual,bulan=short,jumlah=n)
membership$bulan <- month.name[membership$bulan]
ggplot(membership, aes(x=bulan, y=jumlah, fill=status)) +
  geom_col(position = "dodge") +
  labs(title = "Grafik Jumlah Premium dan Casual Setiap Bulan",
       x = "Bulan",
       y = "Jumlah") +
  scale_fill_manual(values = c("#F8766D", "#00BFC4"), labels = c("Casual", "Member"))

##ini terjadi karena moth dalam bentuk char bukan date time
