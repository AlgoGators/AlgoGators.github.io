# The Backadjustment Transformation
The backadjustment transformation is the process of adjusting historical data to account for roll dates and contract expirations. When a futures contract expires, the price of the contract can change dramatically due to the change in demand for the contract. Assuming that there is no change in the underlying asset, the price of the contract should remain constant. However, in reality, the price of the contract can change due to the change in demand for the contract. This change in price can lead to a distortion in the historical data, making it difficult to analyze the data accurately.

We begin the backadjustment transformation by identifying the roll dates for each futures contract. Within our raw dataframe, we have the `instrument_id` and the `expiration` columns. The `instrument_id` column contains the unique identifier for each futures contract, while the `expiration` column contains the expiration date for each contract. We use this information to identify the roll dates for each contract.

We can find the roll dates by locating where the previous entry does not match the current entry in the `instrument_id` column. This indicates that a new contract has been rolled into the dataset. We then adjust the price of the new contract to match the price of the old contract on the roll date. This process ensures that the historical data remains consistent and accurate. The example below demonstrates how we locate the roll dates for the `cl` contract dataframe.

```python
cl.loc[cl['instrument_id_data'].shift() != cl['instrument_id_data']]['expiration']
```

Once we have identified the roll dates, we adjust the price of the new contract to match the price of the old contract on the roll date. There are several methods for backadjusting the price of the new contract, including the ratio method, the difference method, and the percentage method. The backadjustment transformation employs the panama canal method, which adjusts the price of historical data to match the front contract price on the roll date. This method accurately reflects the price of the most current contract. We also employ the use of roll factors based on calendar days and not open interest.

The backadjustment transformation is performed on each futures contract in the dataset. Using `Pandas`, we iterate through each contract and adjust the price of the new contract to match the price of the old contract on the roll date. We apply the float adjustment factor to each subsequent contract to ensure that the historical data remains consistent and accurate. We then add to this factor the difference between the old contract price and the new contract price on the roll date. This process ensures that the historical data is adjusted correctly and accurately reflects the price of the most current contract.

The following code snippet demonstrates how we perform the backadjustment transformation on the `cl` contract dataframe.

```python
def backadjust(raw: pd.DataFrame) -> pd.DataFrame:
    adjustment: float = 0.0
    # Flip the dataframe so we can iterate from the most recent contract
    raw = raw[::-1]
    # Add a column to the dataframe that will hold the adjusted prices and adjustment
    raw['open_adj'] = raw['open']
    raw['high_adj'] = raw['high']
    raw['low_adj'] = raw['low']
    raw['close_adj'] = raw['close']
    raw['adjustment'] = 0.0
    # Iterate through the dataframe
    for i in range(1, len(raw)):
        # If the instrument_id changes
        if raw['instrument_id_data'].iloc[i] != raw['instrument_id_data'].iloc[i - 1]:
            # Calculate the differential
            adjustment += raw['close'].iloc[i - 1] - raw['open'].iloc[i]
        # Apply the adjustment to the prices
        raw['open_adj'].iloc[i] += adjustment
        raw['high_adj'].iloc[i] += adjustment
        raw['low_adj'].iloc[i] += adjustment
        raw['close_adj'].iloc[i] += adjustment
        # Save the adjustment
        raw['adjustment'].iloc[i] = adjustment
    
    return raw[::-1]
```

The backadjustment transformation function takes the raw dataframe as input and returns the backadjusted dataframe. The process is performed on each futures contract in the dataset, ensuring that the historical data remains consistent and accurate. The backadjusted data is now ready for further analysis and modeling.