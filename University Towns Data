import pandas as pd
import numpy as np
from scipy.stats import ttest_ind

# Definitions:
# * A _quarter_ is a specific three month period, Q1 is January through March, Q2 is April through June, Q3 is July through September, Q4 is October through December.
# * A _recession_ is defined as starting with two consecutive quarters of GDP decline, and ending with two consecutive quarters of GDP growth.
# * A _recession bottom_ is the quarter within a recession which had the lowest GDP.
# * A _university town_ is a city which has a high percentage of university students compared to the total population of the city.
 
# **Hypothesis**: University towns have their mean housing prices less effected by recessions. 
# * Run a t-test to compare the ratio of the mean price of houses in university towns the quarter before the recession starts compared to the recession bottom. (`price_ratio=quarter_before_recession/recession_bottom`)

# The following data files are available:
# * From the [Zillow research data site](http://www.zillow.com/research/data/) there is housing data for the United States. In particular the datafile for [all homes at a city level](http://files.zillowstatic.com/research/public/City/City_Zhvi_AllHomes.csv), ```City_Zhvi_AllHomes.csv```, has median home sale prices at a fine grained level.
# * From the Wikipedia page on college towns is a list of [university towns in the United States](https://en.wikipedia.org/wiki/List_of_college_towns#College_towns_in_the_United_States) which has been copy and pasted into the file ```university_towns.txt```.
# * From Bureau of Economic Analysis, US Department of Commerce, the [GDP over time](http://www.bea.gov/national/index.htm#gdp) of the United States in current dollars (use the chained value in 2009 dollars), in quarterly intervals, in the file ```gdplev.xls```. For this assignment, only look at GDP data from the first quarter of 2000 onward.

# Use this dictionary to map state names to two letter acronyms
states = {'OH': 'Ohio', 'KY': 'Kentucky', 'AS': 'American Samoa', 'NV': 'Nevada', 'WY': 'Wyoming', 'NA': 'National', 'AL': 'Alabama', 'MD': 'Maryland', 'AK': 'Alaska', 'UT': 'Utah', 'OR': 'Oregon', 'MT': 'Montana', 'IL': 'Illinois', 'TN': 'Tennessee', 'DC': 'District of Columbia', 'VT': 'Vermont', 'ID': 'Idaho', 'AR': 'Arkansas', 'ME': 'Maine', 'WA': 'Washington', 'HI': 'Hawaii', 'WI': 'Wisconsin', 'MI': 'Michigan', 'IN': 'Indiana', 'NJ': 'New Jersey', 'AZ': 'Arizona', 'GU': 'Guam', 'MS': 'Mississippi', 'PR': 'Puerto Rico', 'NC': 'North Carolina', 'TX': 'Texas', 'SD': 'South Dakota', 'MP': 'Northern Mariana Islands', 'IA': 'Iowa', 'MO': 'Missouri', 'CT': 'Connecticut', 'WV': 'West Virginia', 'SC': 'South Carolina', 'LA': 'Louisiana', 'KS': 'Kansas', 'NY': 'New York', 'NE': 'Nebraska', 'OK': 'Oklahoma', 'FL': 'Florida', 'CA': 'California', 'CO': 'Colorado', 'PA': 'Pennsylvania', 'DE': 'Delaware', 'NM': 'New Mexico', 'RI': 'Rhode Island', 'MN': 'Minnesota', 'VI': 'Virgin Islands', 'NH': 'New Hampshire', 'MA': 'Massachusetts', 'GA': 'Georgia', 'ND': 'North Dakota', 'VA': 'Virginia'}


def get_list_of_university_towns():
    '''Returns a DataFrame of towns and the states they are in from the 
    university_towns.txt list. The format of the DataFrame should be:
    DataFrame( [ ["Michigan", "Ann Arbor"], ["Michigan", "Yipsilanti"] ], 
    columns=["State", "RegionName"]  )
    
    The following cleaning needs to be done:

    1. For "State", removing characters from "[" to the end.
    2. For "RegionName", when applicable, removing every character from " (" to the end.
    3. Depending on how you read the data, you may need to remove newline character '\n'. '''
    with open("university_towns.txt","r") as f:
        town=f.readlines()
    def save(state,city,dic):
        if state in dic:
            dic[state].append(city)
        else:
            dic[state]=[]

    dic = {}
    state=''

    for n in town:
        if "[edit]" in n:
            act_state=n.replace("[edit]","").strip()
            save(act_state,"",dic)
            state=n.replace("[edit]","").strip()
        else:
            city=n.split("(")[0].strip()
            save(state,city,dic)
    university=[]
    for State,RegionName in dic.items():
        for city in RegionName:
            university.append({"State":State,"RegionName":city})
    university=pd.DataFrame(university)
    return university
get_list_of_university_towns()

def get_recession_start():
    '''Returns the year and quarter of the recession start time as a 
    string value in a format such as 2005q3'''
    GDP=pd.read_excel('gdplev.xls',skiprows=5,header=0) #how to use usecols
    GDP=GDP.iloc[2:,4:-1]
    GDP.rename(columns={"Unnamed: 4":'qr'},inplace=True)

    number=GDP[GDP['qr']=="2000q1"].index[0]
    GDP=GDP.loc[number:]
    GDP.reset_index(inplace=True,drop=True)
    GDP['Next_qr']=list(GDP["GDP in billions of chained 2009 dollars.1"].iloc[1:])+[np.NAN]
    GDP['Two_qr']=list(GDP["GDP in billions of chained 2009 dollars.1"].iloc[2:])+2*[np.NAN]

    GDP['begin']=(GDP["GDP in billions of chained 2009 dollars.1"]>GDP['Next_qr'] )& (GDP['Next_qr']>GDP['Two_qr'])
    GDP.head()
    start=GDP[GDP['begin']==True]
    return start['qr'].values[0]
get_recession_start()

def get_recession_end():
    '''Returns the year and quarter of the recession end time as a 
    string value in a format such as 2005q3'''
    GDP=pd.read_excel('gdplev.xls',skiprows=5,header=0) #how to use usecols
    GDP=GDP.iloc[2:,4:-1]
    GDP.rename(columns={"Unnamed: 4":'qr'},inplace=True)

    number=GDP[GDP['qr']=="2000q1"].index[0]
    GDP=GDP.loc[number:]
    GDP.reset_index(inplace=True,drop=True)    
    GDP['last_qr']=[np.NAN]+list(GDP["GDP in billions of chained 2009 dollars.1"].iloc[:-1])
    GDP['lasttwo_qr']=2*[np.NAN]+list(GDP["GDP in billions of chained 2009 dollars.1"].iloc[:-2])

    GDP['end']=(GDP["GDP in billions of chained 2009 dollars.1"]>GDP['last_qr'] )& (GDP['last_qr']>GDP['lasttwo_qr']) & (GDP['qr']>get_recession_start())
    GDP.head()
    end=GDP[GDP['end']==True]       
    return end['qr'].values[0]
get_recession_end()

def get_recession_bottom():
    '''Returns the year and quarter of the recession bottom time as a 
    string value in a format such as 2005q3'''
    GDP=pd.read_excel('gdplev.xls',skiprows=5,header=0) #how to use usecols
    GDP=GDP.iloc[2:,4:-1]
    GDP.rename(columns={"Unnamed: 4":'qr'},inplace=True)

    number=GDP[GDP['qr']=="2000q1"].index[0]
    GDP=GDP.loc[number:]
    GDP.reset_index(inplace=True,drop=True)
    GDP_3=GDP[(GDP['qr'] > get_recession_start())&(GDP['qr'] < get_recession_end())]
    bottom=GDP_3[GDP_3["GDP in billions of chained 2009 dollars.1"]==GDP_3["GDP in billions of chained 2009 dollars.1"].min()]
    
    return bottom['qr'].values[0]
get_recession_bottom()

def convert_housing_data_to_quarters():
    '''Converts the housing data to quarters and returns it as mean 
    values in a dataframe. This dataframe should be a dataframe with
    columns for 2000q1 through 2016q3, and should have a multi-index
    in the shape of ["State","RegionName"].
    
    Note: Quarters are defined in the assignment description, they are
    not arbitrary three month periods.
    
    The resulting dataframe should have 67 columns, and 10,730 rows.
    '''
    housing=pd.read_csv("City_Zhvi_AllHomes.csv")
    housing.head()

    for year in range(2000,2017):
        for quarter in range(1,5):
            if (year==2016) & (quarter == 3):
                break
            new_column='{0}q{1}'.format(year,quarter)
            begin_month=3*quarter-2
            end_month=3*quarter
            begin_column='{0}-{1:02d}'.format(year,begin_month)
            end_column='{0}-{1:02d}'.format(year,end_month)
            housing[new_column]=housing.loc[:,begin_column:end_column].mean(axis=1)
            new_column_name = '{0}q{1}'.format(year, quarter)
            begin_month = (quarter-1)*3 + 1
            end_month = quarter*3
            begin_column = '{0}-{1:02d}'.format(year,begin_month)
            end_column = '{0}-{1:02d}'.format(year,end_month)
    housing['2016q3']=(housing['2016-07']+housing['2016-08'])/2
    housing['State']=housing['State'].apply(lambda x:states[x])
    housing=housing.set_index(['State','RegionName'])
    housing=housing.loc[:,"2000q1":"2016q3"]
    return housing
convert_housing_data_to_quarters()

def run_ttest():
    '''First creates new data showing the decline or growth of housing prices
    between the recession start and the recession bottom. Then runs a ttest
    comparing the university town values to the non-university towns values, 
    return whether the alternative hypothesis (that the two groups are the same)
    is true or not as well as the p-value of the confidence. 
    
    Return the tuple (different, p, better) where different=True if the t-test is
    True at a p<0.01 (we reject the null hypothesis), or different=False if 
    otherwise (we cannot reject the null hypothesis). The variable p should
    be equal to the exact p value returned from scipy.stats.ttest_ind(). The
    value for better should be either "university town" or "non-university town"
    depending on which has a lower mean price ratio (which is equivilent to a
    reduced market loss).'''
    start=   get_recession_start()
    bottom = get_recession_bottom()
    university = get_list_of_university_towns().set_index(["State","RegionName"])    
    housing = convert_housing_data_to_quarters().loc[:,[start,bottom]]
    housing['price%']=(housing[start]-housing[bottom])/housing[start]
    f=housing.index.isin(university.index)
    uni = housing.loc[f==True].dropna()
    non_uni = housing.loc[f==False].dropna()
    answer=ttest_ind(uni['price%'],non_uni['price%'])    
    statistics,pvalue=ttest_ind(uni['price%'],non_uni['price%'])
    outcome=statistics<0
    better=["non-university town","university town"]
    difference=pvalue<0.01
    return (difference,pvalue,better[outcome])
run_ttest()
