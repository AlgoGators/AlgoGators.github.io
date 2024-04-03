# Chapter 1 - The Data Engine
The data engine system works by collecting raw data from our data provider, Databento, and transforming it into a format that can be used by our divergent, convergent, and experiential systems. The data engine kicks off the data pipeline, which is a series of steps that the data goes through to be transformed into a usable format. 

The data engine is responsible for the following tasks:
- Collecting raw data from Databento.
- Transforming the raw data into a usable format.
- Storing the transformed data in our database.

## The Raw Data
The raw data consists of two schemas: the `OHLCV-1d` schema and the `definitions` schema. The OHLCV-1d schema contains the open, high, low, close, and volume data for each asset. It also contains the `instrument_id` and is indexed by the `ts_event` field, a timestamp that represents the time of the event. The definitions schema contains the metadata for each asset, such as the expiry date timestamp, the asset type, the asset class, and the other helpful information. Within constructing our data we perform an inner join on the `OHLCV-1d` schema and the `definitions` schema on the `ts_event` index and the `ts_recv` index, respectively.

## The Backadjustment Transformation
The first transformation step involves a process called "backadjustment". This process adjusts the raw price data to account for roll dates, which are the dates when the futures contracts expire. The backadjustment process ensures that the price data is continuous and does not have any gaps or jumps due to the expiration of futures contracts. This step is crucial for maintaining the integrity of the price data and ensuring that it is suitable for analysis and modeling. Backadjustment is not a perfect process, and there are various methods and assumptions involved in determining the correct adjustment factors. However, it is an essential step in preparing the data for further analysis and processing.
