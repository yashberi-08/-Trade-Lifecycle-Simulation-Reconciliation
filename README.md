# -Trade-Lifecycle-Simulation-Reconciliation
 import pandas as pd
import random
from datetime import datetime, timedelta

# Parameters
num_trades = 1000  # Increase the number of trades for a larger dataset
securities = ['AAPL', 'TSLA', 'MSFT', 'GOOGL']
sides = ['Buy', 'Sell']
counterparties = [f'CP{str(i+1).zfill(4)}' for i in range(num_trades)]
statuses = ['Booked', 'Confirmed', 'Settled']

def random_date(start, end):
    return start + timedelta(days=random.randint(0, (end - start).days))

# Generate Internal Trade Data (Sheet 1)
internal_trades = []
start_date = datetime(2025, 5, 1)
end_date = datetime(2025, 5, 31)

for i in range(1, num_trades + 1):
    trade_date = random_date(start_date, end_date)
    quantity = random.randint(10, 1000)
    price = round(random.uniform(100, 500), 2)
    trade = {
        'Trade ID': f'T{i:04d}',
        'Trade Date': trade_date.strftime('%Y-%m-%d'),
        'Security': random.choice(securities),
        'Side': random.choice(sides),
        'Quantity': quantity,
        'Price': price,
        'Counterparty': counterparties[i-1]
    }
    internal_trades.append(trade)

internal_df = pd.DataFrame(internal_trades)
internal_df.to_csv('sheet1_internal_trades.csv', index=False)

# Generate Counterparty Data (Sheet 2) with some mismatches
counterparty_trades = []
for idx, row in internal_df.iterrows():
    # 85% match, 15% have a random mismatch in quantity or price
    if random.random() < 0.85:
        quantity = row['Quantity']
        price = row['Price']
    else:
        quantity = row['Quantity'] + random.choice([-5, 5, 10, -10])
        price = round(row['Price'] + random.choice([-2, 2, 5, -5]), 2)
    cp_trade = {
        'Counterparty': row['Counterparty'],
        'Trade Date': row['Trade Date'],
        'Security': row['Security'],
        'Side': row['Side'],
        'Quantity': quantity,
        'Price': price,
        'Our Trade ID': row['Trade ID']
    }
    counterparty_trades.append(cp_trade)

counterparty_df = pd.DataFrame(counterparty_trades)
counterparty_df.to_csv('sheet2_counterparty_trades.csv', index=False)

print("Generated 'sheet1_internal_trades.csv' and 'sheet2_counterparty_trades.csv' with 1000 trades each.")
