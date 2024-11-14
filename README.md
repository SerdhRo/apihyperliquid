### Hyperliquid API Quick Start Guide

Hyperliquid provides a REST API that allows developers to access trading functionalities, including placing orders, fetching market data, and managing positions. This guide will walk you through the essentials to set up and start interacting with the Hyperliquid API.

---

### Prerequisites

1. **Python 3.x** installed.
2. **Requests** library for HTTP requests. Install it using:
   ```bash
   pip install requests
   ```
3. **API Key**: You’ll need an API key from your Hyperliquid account for authenticated requests.

---

### Step 1: Setting Up the API Client

To start, set up basic functions to handle authentication and make requests to the Hyperliquid API.

```python
import requests
import time
import hashlib
import hmac

# Set your API key and secret here
API_KEY = 'your_api_key'
API_SECRET = 'your_api_secret'
BASE_URL = 'https://api.hyperliquid.xyz'

# Function to generate authentication headers
def get_headers():
    timestamp = str(int(time.time() * 1000))
    signature_payload = f'{timestamp}GET/api/v1/someEndpoint'.encode()
    signature = hmac.new(API_SECRET.encode(), signature_payload, hashlib.sha256).hexdigest()

    headers = {
        'Content-Type': 'application/json',
        'HL-ACCESS-KEY': API_KEY,
        'HL-ACCESS-TIMESTAMP': timestamp,
        'HL-ACCESS-SIGNATURE': signature,
    }
    return headers
```

### Step 2: Testing the Connection

Start by making a simple request to test your connection. Here’s an example that fetches the account’s balance.

```python
def get_balance():
    endpoint = '/api/v1/balance'
    url = BASE_URL + endpoint
    response = requests.get(url, headers=get_headers())
    
    if response.status_code == 200:
        return response.json()
    else:
        return f"Error: {response.status_code} - {response.text}"

# Run the function
print(get_balance())
```

If successful, this should return your account balance data.

---

### Step 3: Fetching Market Data

The Hyperliquid API provides access to real-time market data. Here’s how to fetch the order book data for a specific trading pair.

```python
def get_order_book(symbol):
    endpoint = f'/api/v1/orderbook/{symbol}'
    url = BASE_URL + endpoint
    response = requests.get(url)
    
    if response.status_code == 200:
        return response.json()
    else:
        return f"Error: {response.status_code} - {response.text}"

# Example usage
print(get_order_book('BTC-USD'))
```

This will output the current order book for the BTC-USD trading pair.

---

### Step 4: Placing an Order

To place an order, use an authenticated POST request. Here’s how to place a market or limit order.

```python
def place_order(symbol, side, order_type, quantity, price=None):
    endpoint = '/api/v1/order'
    url = BASE_URL + endpoint
    
    # Data for the order
    data = {
        "symbol": symbol,
        "side": side,      # "buy" or "sell"
        "type": order_type, # "limit" or "market"
        "quantity": quantity
    }
    
    if order_type == "limit":
        data["price"] = price
    
    response = requests.post(url, json=data, headers=get_headers())
    
    if response.status_code == 200:
        return response.json()
    else:
        return f"Error: {response.status_code} - {response.text}"

# Example usage: Place a limit buy order for BTC-USD
print(place_order("BTC-USD", "buy", "limit", 0.01, price=20000))
```

### Step 5: Checking Order Status

After placing an order, you might want to check its status.

```python
def get_order_status(order_id):
    endpoint = f'/api/v1/order/{order_id}'
    url = BASE_URL + endpoint
    response = requests.get(url, headers=get_headers())
    
    if response.status_code == 200:
        return response.json()
    else:
        return f"Error: {response.status_code} - {response.text}"

# Example usage: Check status of a specific order
print(get_order_status("your_order_id"))
```

### Step 6: Cancelling an Order

To cancel an open order, use the following function:

```python
def cancel_order(order_id):
    endpoint = f'/api/v1/order/{order_id}/cancel'
    url = BASE_URL + endpoint
    response = requests.post(url, headers=get_headers())
    
    if response.status_code == 200:
        return response.json()
    else:
        return f"Error: {response.status_code} - {response.text}"

# Example usage: Cancel a specific order
print(cancel_order("your_order_id"))
```

---

### Notes

- **Rate Limiting**: Hyperliquid’s API may have rate limits, so avoid sending excessive requests.
- **Error Handling**: Check `response.status_code` and handle errors as necessary.
