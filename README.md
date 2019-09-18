# scrape-wiki-for-chemical-data

import requests
import pandas as pd
import numpy as np

chem_list = ["Fentanyl", "Remifentanyl", "Morphine"] # list of chemicals to iterate through
value_dict = {} # create dict for storing name and result(molar mass)
for item in chem_list:   #iterate through chem_list
    url = "https://en.wikipedia.org/wiki/{}".format(item) #sequentially take the name in chem list
    response = str(requests.get(url)) #check the HTML response before attempting to read, convert the result to string
    respo_2 = response[-5:-2] #take only the section of the response we need to convert to int
    int_resp = int(respo_2) # cast to int
    if int_resp == 200: #check if the HTML response is correct, if so extract data
        table_list = pd.read_html(url) #create a list of the tables in the web page
        df_chemprop = pd.DataFrame(table_list[0]) # grab the specific table we need (the 1st table)
        df_chemprop.rename(columns={0:"Properties", 1:"Values"}, inplace=True) #rename columns
        df_chemprop.set_index(["Properties"], inplace=True) #set the index to properties so you can use the label locator .loc
        molar_mass = df_chemprop.loc["Molar mass"] # pull entry for "Molar mass" row assign to variable "molar_mass"       
        value_dict[item] = molar_mass #assign value molar_mass to key[item]
    else:
        value_dict[item] = np.NaN #if connection does not work (no page, spelling mistake, assign NaN)
print(value_dict)
chem_done = pd.DataFrame(value_dict).T
chem_done
