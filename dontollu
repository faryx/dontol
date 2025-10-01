import requests
import json
import threading
import time
from concurrent.futures import ThreadPoolExecutor

# Configuration
cookie_token = "new_bbs_serviceToken=%2FNwpNAjtDz1VdXwWY%2FsysEkwo9gO8BnxzL1hlS8Ml2%2FmuW3DHyeJlzaR86%2FbPYUR1OlF%2F0We5E%2ByekyT5i1rJ9jt%2BThpUK3gd92naWJtWZzfLcDjTyd4kXNSD%2FYdmozQU6jC%2F0DSoxQXKxHa%2BpKP4bIjKR4SQ2uHlb60NEiFix8%3D;versionCode=500426;versionName=5.4.26;deviceId=10481EB285707B39A7766E2EC5CF5F7404934EA1;"

# URLs
check_url = "https://sgp-api.buy.mi.com/bbs/api/global/user/data"
auth_url = "https://sgp-api.buy.mi.com/bbs/api/global/apply/bl-auth"

# Headers for checking token
check_headers = {
    'User-Agent': "okhttp/4.12.0",
    'Accept': "application/json",
    'Accept-Encoding': "gzip",
    'Cookie': cookie_token
}

# Headers for bl-auth request
auth_headers = {
    'User-Agent': "okhttp/4.12.0",
    'Accept': "application/json",
    'Accept-Encoding': "gzip",
    'Content-Type': "application/json",
    'content-type': "application/json; charset=utf-8",
    'Cookie': cookie_token
}

auth_payload = {
    "is_retry": True
}

# Global variables for tracking
total_requests = 0
successful_requests = 0
failed_requests = 0
start_time = time.time()
lock = threading.Lock()

# Check token validity first
print("🔍 Checking token validity...")
response = requests.get(check_url, headers=check_headers)

try:
    data = json.loads(response.text)
    
    if data.get("code") == 100004 and data.get("msg") == "need login":
        print("❌ TOKEN EXPIRED - Need to login again")
        print("Script will exit...")
        exit()
    elif data.get("code") == 0 and data.get("msg") == "success":
        print("✅ TOKEN VALID - Starting high-speed requests...")
        user_data = data.get("data", {})
        if user_data:
            print(f"User ID: {user_data.get('user_id', 'N/A')}")
    else:
        print(f"⚠️ UNKNOWN STATUS - Code: {data.get('code')}, Message: {data.get('msg')}")
        print("Script will exit...")
        exit()
        
except json.JSONDecodeError:
    print("❌ ERROR - Invalid JSON response")
    exit()
except Exception as e:
    print(f"❌ ERROR - {str(e)}")
    exit()

print("\n🚀 Starting ULTRA-FAST multi-threaded requests...")
print("🔥 Running 50 concurrent threads for maximum speed!")
print("Press Ctrl+C to stop\n")

# Create session for connection pooling
session = requests.Session()

# Worker function for threads
def worker():
    global total_requests, successful_requests, failed_requests
    
    while True:
        try:
            # Make request with session for speed
            response = session.post(auth_url, json=auth_payload, headers=auth_headers, timeout=5)
            
            with lock:
                total_requests += 1
                current_request = total_requests
            
            response_text = response.text
            
            # Single line format
            print(f"[REQ {current_request}] RESPONSE: {response_text}")
            
            try:
                result = json.loads(response_text)
                if result.get("code") == 0:
                    with lock:
                        successful_requests += 1                    
                else:
                    with lock:
                        failed_requests += 1
                    
            except json.JSONDecodeError:
                with lock:
                    failed_requests += 1
                
        except Exception as e:
            with lock:
                failed_requests += 1
            print(f"❌ Request ERROR - {str(e)}")
            print("-" * 50)

# Create and start 50 threads for maximum speed
threads = []
for i in range(1):
    thread = threading.Thread(target=worker)
    thread.daemon = True
    thread.start()
    threads.append(thread)

print(f"🚀 Started {len(threads)} threads!")

# Keep main thread alive and handle Ctrl+C
try:
    while True:
        time.sleep(1)
        
except KeyboardInterrupt:
    elapsed_time = time.time() - start_time
    rate = total_requests / elapsed_time if elapsed_time > 0 else 0
    print(f"\n\n🛑 STOPPED BY USER")
    print(f"📊 FINAL STATISTICS:")
    print(f"Total Requests: {total_requests}")
    print(f"Successful: {successful_requests}")
    print(f"Failed: {failed_requests}")
    print(f"Success Rate: {(successful_requests/total_requests*100):.1f}%" if total_requests > 0 else "0%")
    print(f"Average Rate: {rate:.2f} requests/second")
    print(f"Total Runtime: {elapsed_time:.1f} seconds")
