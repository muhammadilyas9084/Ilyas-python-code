import platform 
import cpuinfo
import psutil
import subprocess 
import win32api
import speedtest
import socket
import requests



#software list part 
Data = subprocess.check_output(['wmic', 'product', 'get', 'name']) 
a = str(Data) 
print("Software List - ")
   
try: 
    
    
    for i in range(len(a)): 
        print(a.split("\\r\\r\\n")[6:][i]) 
  
except IndexError as e: 
    print("")

#internet speed part part 
st = speedtest.Speedtest()
st.get_best_server()
download_speed = st.download() / (1024 * 1024)  # Convert bytes to megabits
upload_speed = st.upload() / (1024 * 1024)  # Convert bytes to megabits
print(f"Download speed: {download_speed:.2f} Mbps")
print(f"Upload speed: {upload_speed:.2f} Mbps")

#screen size resolutions part
screen_width = win32api.GetSystemMetrics(0)
screen_height = win32api.GetSystemMetrics(1)
print(f"Screen size and resolution: {screen_width}x{screen_height}") 

#process Info part
print("CPU model = ",cpuinfo.get_cpu_info()["brand_raw"])
print("Number of cores = ",cpuinfo.get_cpu_info()["count"])

#ram info part
ram_size = psutil.virtual_memory().total / (1024 * 1024 * 1024)
print(f"Ram size = {ram_size} GB" )


#to get public ip address
def get_public_ip():
    """Retrieves the public IP address using multiple methods."""

    methods = [
        _get_public_ip_socket,
        _get_public_ip_requests,
    ]

    for method in methods:
        public_ip = method()
        if public_ip:
            return public_ip

    return None

def _get_public_ip_socket():
    """Retrieves the public IP address using the socket module."""

    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
        s.connect(("8.8.8.8", 80))  # Connect to a Google DNS server
        public_ip = s.getsockname()[0]
        s.close()
        return public_ip
    except Exception:
        pass

def _get_public_ip_requests():
    """Retrieves the public IP address using the requests module."""

    try:
        response = requests.get("https://api.ipify.org")
        response.raise_for_status() 
        public_ip = response.text
        return public_ip
    except Exception:
        pass

if __name__ == "__main__":
    public_ip = get_public_ip()
    if public_ip:
        print("Public IP address:", public_ip)
    else:
        print("Failed to retrieve public IP address.")


# Mac address info
def get_wifi_mac_address():
    """Retrieves the Wi-Fi MAC address."""

    try:
        interfaces = psutil.net_if_addrs()
        for interface_name, interface_addresses in interfaces.items():
            for address in interface_addresses:
                if address.family == psutil.AF_LINK and interface_name.startswith("wlan"):
                    return address.address  # Found the Wi-Fi MAC address
    except Exception as e:
        print(f"Error retrieving Wi-Fi MAC address: {e}")
        return None

if __name__ == "__main__":
    mac_address = get_wifi_mac_address()
    if mac_address:
        print("Wi-Fi MAC address:", mac_address)
    else:
        print("Wi-Fi MAC address not found.")

# Get system information
system = platform.uname()
os_version = f"{system.system} {system.release}"

