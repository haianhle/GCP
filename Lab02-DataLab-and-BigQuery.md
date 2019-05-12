# [ML on GCP C1] Analyzing data using Datalab and BigQuery 

This lab illustrates how to carry out data exploration of large datasets, but continue to use familiar tools like Pandas and Juypter.
The "trick" is to do the first part of the aggregation in BigQuery, get back a Pandas dataset and then work with the smaller Pandas dataset locally. Cloud Datalab provides a managed Jupyter experience, so that it is not necessary to run notebook servers locally.

## Objectives: Analyze a large (70 million rows, 8 GB) airline dataset using Google BigQuery and Cloud Datalab
* Launch Cloud Datalab

  From Cloud Shell
  ```
  gcloud compute zones list
  datalab create mydatalabvm --zone us-central1-c
  ```

* Invoke a BigQuery query

  Try running a query:
  ```
  #standardSQL
  SELECT
      departure_delay,
      COUNT(1) AS num_flights,
      APPROX_QUANTILES(arrival_delay, 5) AS arrival_delay_quantiles
  FROM
      `bigquery-samples.airline_ontime_data.flights`
  GROUP BY
      departure_delay
  HAVING
      num_flights > 100
  ORDER BY
      departure_delay ASC
  ```

  Write a query to find the airport pair (departure and arrival airport) that had the maximum number of flights between them.
  ```
  #standardSQL
  SELECT
      departure_airport,
      arrival_airport,
      COUNT(1) AS num_flights
  FROM
      `bigquery-samples.airline_ontime_data.flights`
  GROUP BY
      departure_airport,
      arrival_airport
  ORDER BY
      num_flights DESC
  LIMIT
      10
  ```
* Create graphs in Datalab

  In a cell Datalab's notebook:
  ```
  query="""
  SELECT
      departure_delay,
      COUNT(1) AS num_flights,
      APPROX_QUANTILES(arrival_delay, 10) AS arrival_delay_deciles
  FROM
      `bigquery-samples.airline_ontime_data.flights`
  GROUP BY
      departure_delay
  HAVING
      num_flights > 100
  ORDER BY
      departure_delay ASC
  """

  import google.datalab.bigquery as bq
  df = bq.Query(query).execute().result().to_dataframe()
  df.head()
  ```

  This gets the results from BigQuery as a Pandas dataframe. Now, we can process the data
  in pandas as normal with the relevant subset of data that is a lot smaller.

  ```
  import pandas as pd
  percentiles = df['arrival_delay_deciles'].apply(pd.Series)
  percentiles = percentiles.rename(columns = lambda x : str(x*10) + "%")
  df = pd.concat([df['departure_delay'], percentiles], axis=1)
  df.head()
  ```

  Plotting data can be done in the notebook as normal.
  ```
  without_extremes = df.drop(['0%', '100%'], 1)
  without_extremes.plot(x='departure_delay', xlim=(-30,50), ylim=(-50,50));
  ```

* Commit and push the notebook to a project git repository
