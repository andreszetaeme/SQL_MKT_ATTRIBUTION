# SQL_MKT_ATTRIBUTION

-> This document is not meant to be the project's documentation. Just an overview of the process and some of the queries used to achieve the solution.

-In this scenario, the client is a Sports Championship that takes place in different countries during the season. 
-They invest in Google and Meta ads to promote each one of these events.
-As part of their strategy, they have 'Generic' and 'Branding' campaigns, in which they promote the Championship and their Brand but not a specific event. Regularly those campaigns are the ones that drive more traffic and sales.

Considering this, they asked us to create an attribution model in which we distribute the 'Generic' and 'Branding' results between each event. 

After a workshop, it was decided that the attribution would be applied between the events active during the same date as the generic and branding campaigns take place. Using the click share as the criteria to distribute the rest of the metrics, like the costs and the revenue. 

This is the process to achieve the solution using SQL:

1) In our main BI platform, we created 2 layers. In the 1st one, we extracted all the data we needed from Google and Meta ads.
   In the second layer, we did the necessary transformations so we could generate a relationship between the Marketing data and the sales data from the back office. 

2) We generated the following tables in the 1st layer. 

![image](https://github.com/andreszetaeme/SQL_MKT_ATTRIBUTION/assets/104032549/acedcd7a-a2a7-47d8-9d9f-c84ef284c681)

Here's the reason for each one:

a) tmp_fc_mkt_campaign_combined: Extraction of all ads data

b) fc_mkt_campaign: Fact table of daily paid media activity. We added new columns slicing the string that contains the campaign name into 'IDs' that we could use later to understand the campaign type, source, language, etc.

![image](https://github.com/andreszetaeme/SQL_MKT_ATTRIBUTION/assets/104032549/b7e259dc-7333-43a1-83a2-d7499be8f3ec)

c) dm_mkt_campaign: Dimension table to relate the campaign ID with the different values we extracted from the campaign name string (campaign type, source, language, etc.). 

d) agg_mkt_campaign_event: Fact table of all the campaigns assigned to an event.

e) agg_mkt_campaign_generic: Fact table of all the campaigns assigned to a generic campaign.

![image](https://github.com/andreszetaeme/SQL_MKT_ATTRIBUTION/assets/104032549/d185bb6d-2fd7-49d8-8370-3d8a30e3916c)

The same principle was used to the rest of the 'agg_mkt_campaign' tables.

f) agg_mkt_campaign_branding: Fact table of all the campaigns assigned to a branding campaign.

g) fc_mkt_campaign_untracked: Fact table of all the campaigns that cannot be assigned. The idea is to identify them and do the proper modification so they can be assigned. 

h) fc_mkt_campaign_cost: We generate a new column with the click share per day. And then multiply that value with the values we want to assign to the campaign. i.e. In this pic, we're doing this query with the costs.

![image](https://github.com/andreszetaeme/SQL_MKT_ATTRIBUTION/assets/104032549/be249ecf-9f72-4c38-924e-27ae8084fcfa)

3) Now, we want to match this Marketing data with the back office, so we can analyze the efficiency of the campaigns with the 'real' sales.
   So, we proceed to create a Star Schema. 

![image](https://github.com/andreszetaeme/SQL_MKT_ATTRIBUTION/assets/104032549/02c6c7e6-63ad-4ed7-b187-5a81ead8e008)

This is the reason for each table:

a) dm_event: Add historical data for each event. We use a Join with another table from the BO. 

![image](https://github.com/andreszetaeme/SQL_MKT_ATTRIBUTION/assets/104032549/ccd5c196-72e4-4967-9c12-a383899c16fd)

b) dm_product: Add sales data at product level. We also use a Join with another table from the BO. 

c) dm_values: To calculate revenue and different kinds of costs, we assigned in a Google Sheet a positive or negative value to each type of transaction.

![image](https://github.com/andreszetaeme/SQL_MKT_ATTRIBUTION/assets/104032549/c725b487-b6b2-428d-89c5-272c384fefb4)

d) dm_mkt_campaign: Bring back the table we created in layer 2.

e) dm_calendar_event: Table to retrieve dates and calculate different periods like days/weeks before the event and season/year to date.

![image](https://github.com/andreszetaeme/SQL_MKT_ATTRIBUTION/assets/104032549/d636bdd4-d27d-4511-abb8-d92effa3ff23)

f) f_fact_transposed: Concatenate fact tables in a single fact table for the final data model.

Finally, this allowed us to generate visuals as the following. This helped us understand the efficiency of the campaigns, after attributing the impact of the 'Generic' and 'Branding' campaigns.

*Events' labels (below each bar) have been hidden intentionally because of confidentiality. 

![image](https://github.com/andreszetaeme/SQL_MKT_ATTRIBUTION/assets/104032549/6f797cfb-53d2-4bab-b5ca-f4ca329b6285)

Thanks!
