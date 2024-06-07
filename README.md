# Power-BI-Matrix-Conditional-Formatting

In this post, I would like to share the solution I found for a task a couple of weeks ago.
 
In Power BI, when the story is supposed to be in such a way that the user first goes to see which of the rows or columns of a matrix have a higher or lower total value, and then looks inside the matrix to see the highest and lowest values related to that row or column. When using conditional formatting of the background color type for such stories, the desired behavior would be to determine the color of each part of the matrix (internal values, row totals, column totals) according to the maximum and minimum of that part, not the others. In addition, in order to improve visual differentiation, it would be better for row and column totals to be different from the internal cells so that the user can reach the desired internal cell with minimal effort. 
 
In Conditional formatting > Background color > Gradient > Apply to: Values and totals, the values of Minimum and Maximum sections must be entered manually. Two problems arise here. First, fixed numbers cannot be suitable for changes over time or changes imposed by external filters such as slicers or other visuals. Second, all of the matrix sections (internal values, row totals, column totals) use the same scale. In Conditional formatting > Background color > Gradient, there is an option that allows the developer to write a measure that calculates the color of each cell separately.
 
For writing this measure, we need to know how computers code colors, in addition to the DAX language. I will give a short explanation here about RGB and HEX, since detailed explanations can be found on many websites. There are red, green and blue color channels in both of these two standards. In RGB, each color takes a value between 0 and 255 (from 2^0 to 1-2^16) in each of the channels. The higher the value of a channel, the deeper the color of that channel. For example, pure red is represented by the triplet (255,0,0) and pure blue by (0,0,255). White is obtained by the combination of all colors (255,255,255) and black is equal to the absence of any of the colors (0,0,0). For example, green and blue are required to create blue-green, so the color code is (0,255,255). For a darker blue-green, the value of the green and blue channels must be reduced simultaneously (that is, be moved towards black (0,0,0)) such as (0,200,200) or (0,80,80). Or, when going from black to white, all the three channels must be increased by the same amount, such as (30, 30, 30), a very dark gray, or (100, 100, 100), a bit lighter gray. This system can generate approximately 16.5 million distinct colors.
 
The logic behind the HEX standard is the same as RGB’s, but the display format is different. In HEX, channel numbers are written in base 16 (Hexadeimal) and are attached together, preceded by a # sign. It should be noted that a zero is placed before single-digit numbers. In base 16, numbers 0 to 9 are written like base 10, but numbers 10 to 15 are shown with letters A to F. For example, the number 255 in base 16 equals to the value of FF (somehow like 99 in base 10). So white becomes #FFFFFF and black becomes #000000. Our initial blue-green is #00FFFF and the darker blue-greens are #00C8C8 and #005050. The gray colors we talked about become #1E1E1E and #646464. RGB and HEX can be converted to each other using Google’s color picker.
 
The calculations of the measure starts with determining the section of the matrix whose cell is being calculated. It uses ISINSCOPE to differentitate between internal values, row totals or column totals. Then it calculates the maximum and minimum of the current section using CROSSJOIN and ALLSELECTED, and determines the location of the value of the current cell is in the range 0 to 1. The reason for using ALLSELECTED is to take into account the external filters that come from slicers or other visuals. For eliminating the effects of the outer filter context and using slicers just for filtering rows or columns, you can use use ALL instead of ALLSELECTED. The values of the color channels for each of the matrix sections are hardcoded in the measure. The minimum value is always mapped to white, while the maximum is mapped to (90,138,198) for internal cells, and to (248,105,107) for totals. Other values are mapped to intermediate colors. Both colors are taken from Excel’s conditional formatting.
 
Finally, it is the time to convert the obtained RGB color to HEX since Power BI only accepts HEX. Power BI has no built-in functions for doing so, therefore, I used @GregDeckler’s code available here.
 
https://community.fabric.microsoft.com/t5/Quick-Measures-Gallery/DEC2HEX-Decimal-to-Hexadecimal/m-p/1062151
 
Don’t forget to set the Format of the measure equal to Text. Otherwise, the conditional formatting form will not show you this measure and you will not be able to select it!
 
The last point to mention is that I wrote this measure using the AdventureWorks database. You can replace 'DimDate'[CalendarYear] and 'DimSalesTerritory'[SalesTerritoryCountry] with what you used in the matrix’s rows and columns, respectively.
 
The complete code of the measure as well as a Power BI example file can be downloaded from my GitHub page. 

https://github.com/mahyaar-moh/Power-BI-Matrix-Conditional-Formatting 
 

