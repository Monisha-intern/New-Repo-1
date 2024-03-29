# New-Repo-1
NAME: Weather Reporting System - Analysis(Hardware Actuation)

MATLAB Code:

% Read temperature data from a ThingSpeak channel over the past 24 hours 
% to calculate the high and low temperatures and write to another channel. 
   
% Channel 12397 contains data from the MathWorks Weather Station, located 
% in Natick, Massachusetts. The data is collected once every minute. Field 
% 4 contains temperature data. 
   
% Channel ID to read data from 
readChannelID = 1369889; 
% Temperature Field ID 
TemperatureFieldID = 1; 
PressureFieldID = 2;
HumidityFieldID = 3;  
% Channel Read API Key   
% If your channel is private, then enter the read API Key between the '' below: 
readAPIKey = 'TI15UDZ38496FELU'; 
   
% Read temperature data for the last 24 hours from the MathWorks weather 
% station channel. Learn more about the thingSpeakRead function by going to 
% the Documentation tab on the right side pane of this page. 
   
[tempF,timeStamp] = thingSpeakRead(readChannelID,'Fields',TemperatureFieldID, ...
                                                'numDays',1,'ReadKey',readAPIKey); 
[pressF,timeStamp] = thingSpeakRead(readChannelID,'Fields',PressureFieldID, ...
                                                'numDays',1,'ReadKey',readAPIKey); 

avgTemperature = nanmean(tempF);
avgPressure = nanmean(pressF);
    
display(avgTemperature,'Average Temperature for the past 24 hours is');  
display(avgPressure,'Average Pressure for the past 24 hours is');  
 


writeChannelID = 1369893; 
% Enter the Write API Key between the '' below: 
writeAPIKey = 'YMAK01DU9P9V9AOD';   
% Learn more about the thingSpeakWrite function by going to the Documentation tab on 
% the right side pane of this page. 

thingSpeakWrite(writeChannelID,'Fields',[1,2],'Values',{avgTemperature,avgPressure},'WriteKey',writeAPIKey);
alertApiKey = 'TAKP7C5XU9ZEVJ8GFZRXC';
alertUrl = "https://api.thingspeak.com/alerts/send";
options = weboptions("HeaderFields", ["ThingSpeak-Alerts-API-Key", alertApiKey ]);
alertSubject = sprintf("Temperature Notification");
if(avgTemperature>28)
    disp('It is very hot now')
    alertBody='HEAT: It is very hot now';
elseif(avgPressure<970)
    disp('It is a windy day')
    alertBody='WIND: It is windy now';
else
    disp('No update in todays weather')
end
%try  
webwrite(alertUrl , "body", alertBody, "subject", alertSubject, options);
   %catch someException
    %fpri

