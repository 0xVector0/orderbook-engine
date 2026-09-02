 MicroBook$# MicroBook
 MicroBook$
 MicroBook$A simple orderbook implementation in Python. Built this to learn how trading systems work under the hood.
 MicroBook$
 MicroBook$## How the Orderbook Engine Works
 MicroBook$
 MicroBook$The engine maintains two sorted lists:
 MicroBook$- Bids: Buy orders sorted by price (highest first)
 MicroBook$- Asks: Sell orders sorted by price (lowest first)
 MicroBook$
 MicroBook$When you place an order:
 MicroBook$1. Limit orders get added to the appropriate list
 MicroBook$2. Market orders immediately try to match against existing orders
 MicroBook$3. The engine automatically matches compatible orders (when bid price >= ask price)
 MicroBook$
 MicroBook$Example:
 MicroBook$```
 MicroBook$Current orderbook:
 MicroBook$Bids: [(100, 50.0), (50, 49.5)]  # 100 shares at $50, 50 shares at $49.50
 MicroBook$Asks: [(75, 51.0), (25, 52.0)]   # 75 shares at $51, 25 shares at $52
 MicroBook$
 MicroBook$Market buy order for 75 shares:
 MicroBook$- Takes the cheapest ask (75 @ $51)
 MicroBook$- Order gets filled completely
 MicroBook$- Orderbook now has no asks at $51
 MicroBook$```
 MicroBook$
 MicroBook$The matching engine runs in a separate thread and processes orders as they come in. Uses SortedList for fast insertions and lookups.
 MicroBook$  
 MicroBook$## Getting Started
 MicroBook$
 MicroBook$Clone and run:
 MicroBook$```bash
 MicroBook$git clone https://github.com/0xVector0/MicroBook.git
 MicroBook$cd MicroBook
 MicroBook$pip install -r requirements.txt
 MicroBook$python server.py
 MicroBook$```
 MicroBook$
 MicroBook$Server starts on `http://localhost:10000` with WebSocket on `ws://localhost:8765`
 MicroBook$
 MicroBook$## Basic Usage
 MicroBook$
 MicroBook$### Place Orders via HTTP
 MicroBook$```python
 MicroBook$import requests
 MicroBook$
 MicroBook$# Buy limit order
 MicroBook$requests.post('http://localhost:10000/limit-order', 
 MicroBook$              json={'amount': 100, 'price': 50.0, 'type': 'bid'})
 MicroBook$
 MicroBook$# Sell limit order  
 MicroBook$requests.post('http://localhost:10000/limit-order',
 MicroBook$              json={'amount': 50, 'price': 51.0, 'type': 'ask'})
 MicroBook$
 MicroBook$# Market buy
 MicroBook$requests.post('http://localhost:10000/order',
 MicroBook$              json={'amount': 25, 'type': 'buy'})
 MicroBook$
 MicroBook$# Check orderbook
 MicroBook$response = requests.get('http://localhost:10000/orderbook')
 MicroBook$print(response.json())
 MicroBook$```
 MicroBook$
 MicroBook$### Use the Python Client
 MicroBook$```python
 MicroBook$from orderbook.orderbook_client import orderbook_client
 MicroBook$
 MicroBook$client = orderbook_client("localhost", 10000)
 MicroBook$
 MicroBook$# Add orders
 MicroBook$client.new_limit_order(100, 50.0, "bid")
 MicroBook$client.new_limit_order(75, 52.0, "ask") 
 MicroBook$
 MicroBook$# Execute trades
 MicroBook$client.new_order(25, "buy")
 MicroBook$client.new_order(30, "sell")
 MicroBook$
 MicroBook$# View current state
 MicroBook$print(client.get_order_book())
 MicroBook$```
 MicroBook$
 MicroBook$## Live Data Streaming
 MicroBook$
 MicroBook$WebSocket endpoint streams real-time orderbook changes:
 MicroBook$
 MicroBook$```python
 MicroBook$import asyncio
 MicroBook$import websockets
 MicroBook$import json
 MicroBook$
 MicroBook$async def monitor_orderbook():
 MicroBook$    uri = "ws://localhost:8765"
 MicroBook$    async with websockets.connect(uri) as websocket:
 MicroBook$        async for message in websocket:
 MicroBook$            data = json.loads(message)
 MicroBook$            print(f"[{data['timestamp']}] {data['message']}")
 MicroBook$
 MicroBook$asyncio.run(monitor_orderbook())
 MicroBook$```
 MicroBook$
 MicroBook$See `examples/` folder for visualization tools and more usage patterns.
 MicroBook$
 MicroBook$## API Reference
 MicroBook$
 MicroBook$HTTP Endpoints:
 MicroBook$- `POST /limit-order` - Add limit order (amount, price, type: bid/ask)
 MicroBook$- `POST /order` - Execute market order (amount, type: buy/sell)  
 MicroBook$- `GET /orderbook` - Get current orderbook state
 MicroBook$- `GET /logs` - Get trade history
 MicroBook$
 MicroBook$WebSocket: 
 MicroBook$- Real-time trade executions and orderbook updates
 MicroBook$
 MicroBook$## Architecture
 MicroBook$
 MicroBook$Simple three-component design:
 MicroBook$- Core Engine: SortedList-based orderbook with O(log n) insertions
 MicroBook$- HTTP Server: Flask API for order placement and queries
 MicroBook$- WebSocket Server: Real-time streaming of trades and updates
 MicroBook$
 MicroBook$## File Structure
 MicroBook$
 MicroBook$```
 MicroBook$MicroBook/
 MicroBook$├── orderbook/              # Core package
 MicroBook$├── examples/               # Usage examples
 MicroBook$├── server.py               # Main entry point
 MicroBook$└── requirements.txt
 MicroBook$```
 MicroBook$
 MicroBook$## License
 MicroBook$
 MicroBook$This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
