# Data insights for COVID-19 response: resources for producing mobility indicators and analysis from CDR data

## Overview

This repository provides mobile network operators with code and guidelines to produce aggregates from their raw CDR data (Call Detail Records) in the context of the COVID-19 pandemic. A description of each aggregate is provided alongside the code, together with suggestions for how it can be used. For more information about the aggregates, and for additional resources on how to utilise them to produce mobility indicators, please visit [covid19.flowminder.org](https://covid19.flowminder.org).

We have initially focused on producing code and guidelines for mobile network operators that may have limited technical capacity,
especially those in low- and middle-income countries. This means that the code we have provided is both simple to modify,
and also should not be extremely computationally intensive to run. However, we will continually add more resources to the repository,
including material that is suitable for settings where more capacity is available, and where higher phone usage results in higher frequency
data. In these cases, it will be possible to produce more complex outputs and analyses.

Mobile network operators looking to produce the aggregates described in this repository from their own CDR data should start by looking at the ["getting started" section](./getting_started).

## Technical usage

The code will need to be adapted to each MNO's system.
The names of tables and columns will need to be changed, and the code may need to be optimised to suit the structure of the data.

The code has been written assuming that the following tables exist:

`calls` is the call events table, including the columns:

-   `msisdn`: SIM identifier,
-   `call_date`: event date,
-   `call_datetime`: event timestamp,
-   `location_id`: identifier of the cell tower.

`cells` is a table with information about cell towers, including the columns:

-   `cell_id`: identifier of the cell tower,
-   `locality`: the geographic area that the cell tower is in (for example, administrative region). We are able to provide assistance with mapping cell tower locations to administrative (or other) localities, if you do not already have this mapping.

[core_tables.sql](core_tables.sql) contains definitions of the expected `calls` and `cells` tables.

## Content

This repository currently contains SQL code and descriptions for the following aggregates:

1.  [Count of active subscribers per locality](count_subscribers.md)  
2.  [Count of residents (home location counts) per locality](count_residents.md)  
3.  Count of travellers (origin-destination matrix). This aggregate has two variants:  
    - [Variant 1: all pairs of locations](od_matrix_directed_all_pairs.md)  
    - [Variant 2: consecutive pairs of locations](od_matrix_directed_consecutive_pairs.md)  
4.  [Count of travellers (connections triangular matrix)](od_matrix_undirected_all_pairs.md)  
5.  [Count of visits at home and away (home-away matrix)](count_visits_home_away.md)  
6.  [Count of home relocations (home origin-destination matrix)](count_home_relocations.md)  
7.  [Count of subscribers only seen in one locality](count_subscribers_single_locality.md)  
8.  [Count of subscribers only seen in home locality](count_subscribers_home_locality.md)  
9.  [Count of events per locality](count_events.md)  
10. [Count of total active subscribers](total_subscribers.md)

## Calculating aggregates over multiple time intervals and locality sizes

In several of the aggregates defined in this repository, we recommend that, where possible, the aggregates should be calculated over multiple locality sizes (e.g. different levels of administrative divisions) and time intervals (e.g. hour, day, week). 

Any aggregate that counts the number of unique subscribers that have e.g. visited a certain locality is not 'additive', in the sense that aggregates calculated for the smallest locality size or time interval cannot be summed to compute the value for a larger locality size or time interval. For example, Locality A may be composed of smaller subdivisions a1, a2, and a3. We can count the number of unique subscribers that visited a1, a2, and a3. But because some subscribers may have visited both a1 and a2, we cannot simply sum the number of subscribers that visited each subdivision to obtain the number of unique subscribers that visited Locality A (because subscribers will be counted multiple times if they visited multiple subdivisions). A similar reasoning applies to time intervals.

In most cases where we recommend that aggregates should be calculated over more than one time interval, we have only provided the SQL code to calculate the aggregates per day. This code should be modified to calculate aggregates for different time intervals, as appropriate. As an example, in [count_subscribers.sql](count_subscribers.sql) we provide three implementations of the same query, aggregating by day, by week and by hour, respectively. If you have any questions about adapting the other queries to aggregate over different time intervals, please contact the team at covid19@flowminder.org.

## Privacy

All aggregates included in this repository produce aggregated outputs (i.e. one result per locality, not one result per subscriber), to protect subscribers' privacy and personal data. Additionally, to ensure that no locational information about individual subscribers is revealed, only aggregates containing more than 15 subscribers are produced as outputs. The limit of 15 is in line with the standard that is used by many statistics offices.

Note that the queries in [home_locations.sql](home_locations.sql) produce per-subscriber data. These intermediate results should not be shared outside of the MNO's system, and are intended for use only as components of the aggregate queries.

## Feedback

We welcome all suggestions and feedback. Please open an issue, submit a pull request, or contact the team at covid19@flowminder.org.
