# pandas_-project
import requests
import pandas as pd
from datetime import datetime

# Step 1: Specify the CoinGecko API endpoint for historical prices
url = 'https://api.coingecko.com/api/v3/coins/bitcoin/market_chart'

# Step 2: Make an HTTP request to the CoinGecko API with the specified parameters
params = {
    'vs_currency': 'usd',
    'days': '7',
    'interval': 'daily',
# Project Name

This project is a Python script that retrieves historical prices of Bitcoin from the CoinGecko API and performs data analysis using pandas.

## Getting Started

To get started with this project, you'll need to have Python installed on your machine. You can download Python from the official website: https://www.python.org/downloads/

### Installation

1. Clone the repository: `git clone https://github.com/your-username/project.git`
2. Navigate to the project directory: `cd project`
3. Install the required dependencies: `pip install -r requirements.txt`

### Usage

1. Open the `project.py` file in your preferred code editor.
2. Run the script to retrieve Bitcoin historical prices and perform data analysis.
3. The script will generate two CSV files: `bitcoin_prices_week.csv` and `weekly_statistics.csv`.
4. You can find the generated CSV files in the project directory.

### Contributing

Contributions are welcome! If you have any suggestions or improvements, feel free to open an issue or submit a pull request.

### License

This project is licensed under the [MIT License](LICENSE).
response = requests.get(url, params=params) # Make an HTTP GET request
data = response.json() # Convert the response to JSON format

# Step 3: Extract relevant information from the API response
timestamps = [timestamp[0] // 1000 for timestamp in data['prices']]  # Convert milliseconds to seconds
prices = [price[1] for price in data['prices']] # Extract the price from each timestamp
market_caps = [cap[1] for cap in data['market_caps']] # Extract the market cap from each timestamp
total_volumes = [volume[1] for volume in data['total_volumes']] # Extract the total volume from each timestamp

# Step 4: Create a Pandas DataFrame
# Ensure all arrays have the same length
min_length = min(len(timestamps), len(prices), len(market_caps), len(total_volumes)) # Get the minimum length
timestamps = timestamps[:min_length]
prices = prices[:min_length]
market_caps = market_caps[:min_length]
total_volumes = total_volumes[:min_length]

bitcoin_prices_week = pd.DataFrame({
    'Timestamp': timestamps,
    'Date': [datetime.utcfromtimestamp(timestamp).strftime('%Y-%m-%d') for timestamp in timestamps],
    'Price (USD)': prices,
    'Market Cap (USD)': market_caps,
    'Total Volume (USD)': total_volumes,
})

# Convert 'Timestamp' column to datetime and set it as the index
bitcoin_prices_week['Timestamp'] = pd.to_datetime(bitcoin_prices_week['Timestamp'], unit='s')
bitcoin_prices_week.set_index('Timestamp', inplace=True)

# Step 5: Save the DataFrame to a CSV file
bitcoin_prices_week.to_csv('bitcoin_prices_week.csv', index=False)

print('CSV file created successfully.')

# Step 6: Display the DataFrame
print(bitcoin_prices_week)

weekly_statistics = bitcoin_prices_week.resample('w').agg({'Price (USD)': ['min', 'max', 'mean'], 'Market Cap (USD)': 'mean', 'Total Volume (USD)': 'mean'})

# Step 7: Save the DataFrame to a CSV file
weekly_statistics.to_csv('weekly_statistics.csv')

print('-------------------')
print(weekly_statistics)

