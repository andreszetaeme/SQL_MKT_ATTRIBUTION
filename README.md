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

pic 1

Here's the reason for each one:

a) tmp_fc_mkt_campaign_combined: Extraction of all ads data

b) fc_mkt_campaign: Fact table of daily paid media activity. We added new columns slicing the string that contains the campaign name into 'IDs' that we could use later to understand the campaign type, source, language, etc.

pic 2

c) dm_mkt_campaign: Dimension table to relate the campaign ID with the different values we extracted from the campaign name string (campaign type, source, language, etc.). 

d) agg_mkt_campaign_event: Fact table of all the campaigns assigned to an event.

e) agg_mkt_campaign_generic: Fact table of all the campaigns assigned to a generic campaign.

pic 7

The same principle was used to the rest of the 'agg_mkt_campaign' tables.

f) agg_mkt_campaign_branding: Fact table of all the campaigns assigned to a branding campaign.

g) fc_mkt_campaign_untracked: Fact table of all the campaigns that cannot be assigned. The idea is to identify them and do the proper modification so they can be assigned. 

h) fc_mkt_campaign_cost: We generate a new column with the click share per day. And then multiply that value with the values we want to assign to the campaign. i.e. In this pic, we're doing this query with the costs.

pic 3

3) Now, we want to match this Marketing data with the back office, so we can analyze the efficiency of the campaigns with the 'real' sales.
   So, we proceed to create a Star Schema. 

pic 4

This is the reason for each table:

a) dm_event: Add historical data for each event. We use a Join with another table from the BO. 

pic 8

b) dm_product: Add sales data at product level. We also use a Join with another table from the BO. 

c) dm_values: To calculate revenue and different kinds of costs, we assigned in a Google Sheet a positive or negative value to each type of transaction.

pic 5

d) dm_mkt_campaign: Bring back the table we created in layer 2.

e) dm_calendar_event: Table to retrieve dates and calculate different periods like days/weeks before the event and season/year to date.

pic 6

f) f_fact_transposed: Concatenate fact tables in a single fact table for the final data model.

Finally, this allowed us to generate visuals as the following. This helped us understand the efficiency of the campaigns, after attributing the impact of the 'Generic' and 'Branding' campaigns.

*Labels have been hidden intentionally.

pic 9

Thanks!
