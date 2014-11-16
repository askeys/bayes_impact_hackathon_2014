# Department of Labor - Underpaying employers by Nicholas Handel
# Bayes Hackathon November 2014

##################################################################################
##    Settings                        
##################################################################################
inputDirectory = "~"
outputDirectory = "~"
saveImages = TRUE

library(utils)
library(data.table)
library(stringr)
library(forecast)
library(lubridate)

"+" = function(x,y) {
  if(is.character(x) | is.character(y)) {
    return(paste(x , y, sep=""))
  } else {
    .Primitive("+")(x,y)
  }
}


##################################################################################
##    Data                       
##################################################################################

setwd(inputDirectory)

forecast_by_year <- fcst[,sum(value), by = c('st_cd', 'Sector', 'year')]
setnames(forecast_by_year,'st_cd','State')

whd <- as.data.table(read.csv('whd_whisard.csv', stringsAsFactors = F))

zipdata <- as.data.table(read.csv('county_data.csv', stringsAsFactors = F))
setnames(zipdata, c('zcta5', 'County'), c('zip_cd','county'))

naic <- as.data.table(read.csv('naic codes.csv', stringsAsFactors = F))

cc <- as.data.table(read.csv('cc.csv', stringsAsFactors = F))
setnames(cc, c('State.ANSI', 'County.ANSI', 'County.Name'), c("state_code", "county_code", 'county'))
cc$state_code <- paste("0",as.character(cc$state_code),sep= '')
cc$county_code <- paste("00",cc$county_code,sep = '')
cc$county_code <-str_sub(cc$county_code, start = -3, end = -1)
cc$code <- paste(cc$state_code, cc$county_code,sep = '')
cc$county <- str_sub(cc$county, start = 1, end = -8)
cc$county <- paste(cc$county," ", cc$State,sep ='')

countybystate <- as.data.table(read.csv('county_State_Division.csv', stringsAsFactors = F))

# Assign all the NAIC which is not in standard list as Other
d <- whd[,c('zip_cd','st_cd', 'naic_cd', 'case_violtn_cnt', 'cmp_assd_cnt', 'bw_atp_amt', 'findings_start_date', 'findings_end_date'),with=F]
d <- d[st_cd %in% c('AL', 'AK', 'AZ', 'AR', 'CA', 'CO', 'CT', 'DE', 'DC', 'FL', 'GA', 'HI', 'ID', 'IL', 'IN', 'IA', 'KS', 'KY', 'LA', 'ME', 'MD', 'MA', 'MI', 'MN', 'MS', 'MO', 'MT', 'NE', 'NV', 'NH', 'NJ', 'NM', 'NY', 'NC', 'ND', 'OH', 'OK', 'OR', 'PA', 'RI', 'SC', 'SD', 'TN', 'TX', 'UT', 'VT', 'VA', 'WA', 'WV', 'WI', 'WY')]
d[,naic_cd := as.integer(str_sub(as.character(naic_cd),0,2))]
d <- merge(d,naic, by ="naic_cd", all.x = TRUE)

d[,bwcmp := bw_atp_amt + cmp_assd_cnt]
d <- d[bwcmp > 0]

d <- merge(d, zipdata[,c('zip_cd','county'),with=F], by = 'zip_cd') 

#### Forecast
d <- d[,c('zip_cd','st_cd', 'Sector','county','naic_cd', 'bwcmp', 'findings_start_date', 'findings_end_date'),with=F]
d[is.na(Sector),'Sector'] <- 'Other'

d$nmonths <- as.integer((round(as.Date(d$findings_end_date, format = '%m/%d/%y') - as.Date(d$findings_start_date, format = '%m/%d/%y'))/(365.25/12)+1))
d$vmonth <- (d$bwcmp / d$nmonths)

tmp <- as.data.table(expand.grid(unique(d$st_cd),unique(d$Sector),1:192))
fcst <- data.table(st_cd=as.character(tmp$Var1), Sector=as.character(tmp$Var2), month=as.integer(tmp$Var3))
fcst <- fcst[order(st_cd,Sector,month)]
fcst$year <- round(2000 + fcst$month/12 -.51)
fcst$value <- 0

start <- as.Date('01/01/00',format = '%m/%d/%y')
end <- as.Date('01/01/14', format = '%m/%d/%y')
fcst_dates <- seq(as.Date(start, format = '%m/%d/%y'), as.Date(end, format = '%m/%d/%y'), by = "month")
if (TRUE) {
  for (i in 1:(nrow(fcst)/192)) {
    d_fcst <- d[fcst[(i*192-191),1,with=F][[1]] == st_cd][fcst[(i*192-191),2,with=F][[1]] == Sector]
    fcst_set <- data.table(as.double(1:length(fcst_dates))*0)
    for (j in 1:length(fcst_dates)) {
      fcst_set[j] <- d_fcst[(as.Date(findings_start_date, format = '%m/%d/%y') - fcst_dates[j]) <= 0][(as.Date(findings_end_date, format = '%m/%d/%y') - fcst_dates[j]) >= 0][,sum(vmonth)]
    }
    fcst_tmp <- forecast(tryCatch(arima(fcst_set[1:144][[1]], order=c(12,0,3)), error = function(e) {auto.arima(fcst_set[1:156][[1]])}), h = 48)
    #plot(fcst_tmp)
    fcst[(192*i-191):(192*i-24)]$value <- fcst_set[1:168]
    fcst[(i*192-23):(i*192)]$value <- fcst_tmp$mean[25:48]
  }
  fcst[value < 0]$value <- 0
  write.csv(fcst[,1:5,with=F],'forecast.csv')
} else {
  fcst <- read.csv('forecast.csv')
}

setwd(inputDirectory + 'raw_data/')
if (TRUE) {
  for (s in unique(fcst$Sector)) {
    for (y in 2000:2013) {
      fcst_year <- forecast_by_year[Sector == s][year == y]
      county_tmp <- countybystate
      county_tmp <- merge(county_tmp, fcst_year, by='State', all.x = T)
      county_tmp$rate <- county_tmp$PctWorkingPop * county_tmp$V1
      county_tmp$name <- str_sub(county_tmp$County,1,-3)
      county_tmp[,id:=code]
      county_tmp <- county_tmp[,c('id','rate'),with=F]
      write.csv(county_tmp,'metric=Actual Monetary Impact (by year)+industry=' + s +'+year=' + as.character(y) + '.csv',row.names = FALSE)
    }
    for (y in 2014:2015) {
      fcst_year <- forecast_by_year[Sector == s][year == y]
      county_tmp <- countybystate
      county_tmp <- merge(county_tmp, fcst_year, by='State', all.x = T)
      county_tmp$rate <- county_tmp$PctWorkingPop * county_tmp$V1
      county_tmp$name <- str_sub(county_tmp$County,1,-3)
      county_tmp[,id:=code]
      county_tmp <- county_tmp[,c('id','rate'),with=F]
      write.csv(county_tmp,'metric=Forecasted Monetary Impact (by year)+industry=' + s +'+year=' + as.character(y) + '.csv',row.names = FALSE)
    }
  }
}



