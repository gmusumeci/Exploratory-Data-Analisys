install.packages("reshape")
library(reshape)

#This part of the script reads data source data must be stored in wd()
-----------------------------------------------------------------------
NEI <- readRDS("summarySCC_PM25.rds")
SCC <- readRDS("Source_Classification_Code.rds")

#Finding SCC Records only for Coal Combustion sources
-----------------------------------------------------------------------
Search1<-grep("coal",SCC$Short.Name,fixed=TRUE)
Search2<-grep("Coal",SCC$Short.Name,fixed=TRUE) 
Search3<-grep("comb",SCC$Short.Name,fixed=TRUE) 
Search4<-grep("Comb",SCC$Short.Name,fixed=TRUE)
Coal<-c(Search1,Search2)
Comb<-c(Search3,Search4)
CoalComb<-intersect(Coal,Comb)
SCC.Code<-as.character(SCC[CoalComb,1])      

#Subsetting data for Coal Combustion in USA
-----------------------------------------------------------------------
Emissions<-subset(NEI, SCC %in% SCC.Code, na.rm=TRUE)  


#Calculating total PM25 Emission for all source for each year
----------------------------------------------------------------------
EmissionsUSA<-tapply(Emissions$Emissions,as.factor(Emissions$year),sum)
years<-as.numeric(names(EmissionsUSA))

#Plotting Data in an output file
-----------------------------------------------------------------------
png("plot4.png",width=480,height=480,units="px")
barplot(EmissionsUSA/10^3,years,xlab="Year",
        ylab="PM2.5 Emissions (10^3 Tons)",
        main="Total PM2.5 Emissions From Coal Combustion for all US ",col="black",
        ylim=c(0,600))
dev.off()

