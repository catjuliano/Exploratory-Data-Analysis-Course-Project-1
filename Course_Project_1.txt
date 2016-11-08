
if(!file.exists("data")){dir.create("data")} #if file data does not exist it will be created
FileURL <- "http://archive.ics.uci.edu/ml/machine-learning-databases/00235/household_power_consumption.zip"
download.file(FileURL, destfile = "C:/users/rzhp93/Documents/data/housing.zip") #downloads the dataset
unzip(zipfile  = "C:/users/rzhp93/Documents/data/housing.zip", exdir = "./data") #unzips the file

HousingFile <- file("./data/household_power_consumption.txt")
HousingData <- read.table(text = grep("^[1,2]/2/2007",readLines(HousingFile), value = TRUE), 
            col.names = c("Date","Time","Global_active_power", "Global_reactive_power","Voltage", 
            "Global_intensity", "Sub_metering_1", "Sub_metering_2","Sub_metering_3"), sep = ";", header = TRUE) #turns data set into a table from dates 1/2/2007 to 2/2/2007

png(filename = "plot1.png",bg = "transparent", width = 480, height = 480) #creates the plot as a png file 
hist(HousingData$Global_active_power, main = "Global Active Power", col ="red", xlab = "Global Active Power (kilowatts)")
dev.off() #closes the plot

housing_full <- read.csv("./data/household_power_consumption.txt", header = TRUE, na.strings = "?", sep = ";", nrows = 2075259) #gets the full dataset
housing_full$Date <- as.Date(housing_full$Date, format= "%d/%m/%Y") #changes date format to day/month/year
MyData <- subset(housing_full, subset = (Date >= "2007-02-01" & Date <= "2007-02-02")) #only uses dates from 1/2/2007 to 2/2/2007
rm(housing_full)
DateandTime <- paste(as.Date(MyData$Date), MyData$Time) #combines date and time
MyData$DateTime <- as.POSIXct(DateandTime) #creats a date and time column in dataset
plot(MyData$Global_active_power ~ MyData$DateTime, type = "l", xlab = "", ylab = "Global Active Power (kilowatts)") dev.copy(png, file= "plot2.png")
dev.off()


plot(MyData$DateTime, MyData$Sub_metering_1, type = "l", ylab = "Energy sub metering", xlab = "") 
points(MyData$DateTime, MyData$Sub_metering_2, type = "l", col ="red") #adds the second sub_metering
points(MyData$DateTime, MyData$Sub_metering_3, type = "l", col = "blue") #adds the third sub_metering
legend("topright", lty = c(1,1,1), lwd = c(1,1,1), col = c("black","red","blue"), legend = c("Sub_metering_1","Sub_metering_2","Sub_metering_3")) #creates a legend at the top right of graph
dev.copy(png, file="plot3.png") #copies graph to a png file
dev.off() #closes png file

par(mfrow = c(2,2)) #sets the window into 2 rows and 2 columns
plot(MyData$DateTime,MyData$Global_active_power, xlab = "", ylab = "Global Active Power",type ="l") #first plot in top left
plot(MyData$DateTime,MyData$Voltage, xlab = "datetime", ylab = "Voltage", type = "l") #second plot in topright
plot(MyData$DateTime, MyData$Sub_metering_1, type = "l", ylab = "Energy sub metering", xlab = "")  #third plot bottom left
points(MyData$DateTime, MyData$Sub_metering_2, type = "l", col ="red")
points(MyData$DateTime, MyData$Sub_metering_3, type = "l", col = "blue")
legend("topright", lty = c(1,1,1), lwd = c(1,1,1), col = c("black","red","blue"), legend = c("Sub_metering_1","Sub_metering_2","Sub_metering_3"),bty = "n")
plot(MyData$DateTime,MyData$Global_reactive_power, type = "l", xlab = "datetime", ylab = "Global_reactive_power") #fourth plot bottom right
dev.copy(png, file="plot4.png") #copy plot to png
dev.off() #close png
