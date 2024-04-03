# The Raw Data Formation
Our data provider, Databento, provides a plethora of data schemas with varying technical information and timeframes. The two most basic schemas are the `OHLCV-1d` schema and the `definitions` schema. The `OHLCV-1d` schema contains the open, high, low, close, and volume data for each asset on a daily basis. It also contains the `instrument_id` and is indexed by the `ts_event` field, a timestamp that represents the time of the event. The `definitions` schema contains the metadata for each asset, such as the expiry date timestamp, the asset type, the asset class, and other helpful information.

## The OHLCV-1d Schema
Our data engine system collects the `OHLCV-1d` schema from Databento and stores with a dataframe. There are two different approaches to making requests to Databento: the API and the Download Job Submission. The API is used for short-term data requests, while the Download Job Submission is used for long-term data requests, like entire historical datasets on instruments. When adding new instruments to our portfolio, we use the `submit_job` function to request the data from Databento. We then poll the `list_jobs` function until all requested data is available. Once the data is available, we download it using the `download` function and store it. The second approach is to use the API to request data for a specific instrument. We use the `get_range_async` function to request the data and store it in a dataframe. This approach is used for updating the data on a daily basis.

## The Definitions Schema
The `definitions` schema contains the metadata for each asset, such as the expiry date timestamp, the asset type, the asset class, and other helpful information. We use all of the same functions as we do for the `OHLCV-1d` schema to request and store the data but with the `definitions` schema. The `definitions` schema is used to join with the `OHLCV-1d` schema to create a complete dataset for each asset. The `ts_event` index is used to join the two schemas together.

## Joining the Schemas and Storing the Data
The raw data is stored in two separate dataframes: one for the `OHLCV-1d` schema and one for the `definitions` schema using the `Pandas` library. We perform an inner join on the two dataframes using the `ts_event` index and the `ts_recv` index, respectively. We then store the joined data in a parquet file for further processing. We avoid storing the data in a database because it contains a lot of redundant information that is not needed for our analysis. The parquet file is a columnar storage format that is efficient for reading and writing large datasets. The raw data is now ready for the backadjustment transformation, which is the next step in the data pipeline.

```python
raw = pd.merge(data, definitions, how='inner', left_index=True, right_on=definitions.index, suffixes=('_data', '_definition'))
raw.to_parquet(f'raw/{symbol}_full.parquet')
```