to access and controol influx db from its originated dashboard you can use this url:


ip(srvr):port(influx)
ip:8086

structure of the influx db line protocol:

<measurement>,<tag_key>=<tag_value> <field_key>=<field_value> <timestamp>

sample of data filing via pythoon:

```python
from influxdb_client import InfluxDBClient, Point, WritePrecision
from influxdb_client.client.write_api import SYNCHRONOUS

client = InfluxDBClient(url="http://localhost:8086", token="your-token", org="your-org")
write_api = client.write_api(write_options=SYNCHRONOUS)

point = Point("system_metrics") \
    .tag("host", "server1") \
    .tag("region", "us-west") \
    .field("cpu_usage", 75.4) \
    .field("memory_usage", 2048) \
    .time(datetime.utcnow(), WritePrecision.NS)

write_api.write(bucket="your-bucket", record=point)
```


# INSTALLING AND CONFIGURATION OF INFLUXDB CLI

**Influx CLI shell version is 1.6.7~rc0**, is outdated and incompatible with InfluxDB 2.x.

Let’s install **InfluxDB 2.x**.

---

## **Step 1: Install the Correct InfluxDB 2.x CLI**

The `influx` CLI version 1.6.7 is for **InfluxDB 1.x**, and it won’t work with **InfluxDB 2.x**. You need to install the **InfluxDB 2.x CLI**.

### **Install the InfluxDB 2.x CLI on Ubuntu**
1. **Download the CLI**:
   ```bash
   wget https://dl.influxdata.com/influxdb/releases/influxdb2-client-2.7.3-linux-amd64.tar.gz
   ```

2. **Extract the Archive**:
   ```bash
   tar xvzf influxdb2-client-2.7.3-linux-amd64.tar.gz
   ```

3. **Move the Binary to `/usr/local/bin`**:
   ```bash
   sudo mv influx /usr/local/bin/
   ```

4. **Verify the Installation**:
   ```bash
   influx version
   ```
   You should see something like:
   ```
   Influx CLI 2.7.3 (git: fe6d7e6) build_date: 2023-10-10T16:30:34Z
   ```

---

## **Step 2: Configure the InfluxDB 2.x CLI**

Now that you have the correct CLI, let’s configure it to connect to your InfluxDB 2.x server.

1. **Set Up a Configuration**:
   ```bash
    influx config create \
    -n NAME_OF_CONFIG \
    -u http://localhost:8086 \
    -o ORG_NAME \
    -t YOUT_TOKEN_HERE
   ```

2. **Verify the Configuration**:
   ```bash
   influx config ls
   ```

3. **Test the Connection**:
   ```bash
   influx ping
   ```
   If successful, you’ll see:
   ```json
   {
     "version": "2.6.1",
     "status": "ready"
   }
   ```

---

## **Step 3: Useful Commands for InfluxDB 2.x**

Now that you have the correct CLI installed and configured, here are the essential commands:

### **1. General Commands**
- **Check Version**:
  ```bash
  influx version
  ```

- **Ping the Server**:
  ```bash
  influx ping
  ```

- **List Organizations**:
  ```bash
  influx org list
  ```

- **List Buckets**:
  ```bash
  influx bucket list
  ```

- **List Tokens**:
  ```bash
  influx auth list
  ```

---

### **2. Writing Data**
- **Write Data Using Line Protocol**:
  ```bash
  influx write --bucket your-bucket --precision ns "measurement,tag=value field=value timestamp"
  ```
  Example:
  ```bash
  influx write --bucket my-bucket --precision ns "cpu,host=server1 usage=75.4 1696166400000000000"
  ```

- **Write Data from a File**:
  ```bash
  influx write --bucket your-bucket --file /path/to/data.txt
  ```

---

### **3. Querying Data**
- **Run a Flux Query**:
  ```bash
  influx query 'from(bucket: "your-bucket") |> range(start: -1h) |> filter(fn: (r) => r._measurement == "cpu")'
  ```

- **Export Query Results to CSV**:
  ```bash
  influx query 'from(bucket: "your-bucket") |> range(start: -1h)' --raw > output.csv
  ```

---

### **4. Managing Data**
- **Delete Data**:
  ```bash
  influx delete --bucket your-bucket --start 2023-01-01T00:00:00Z --stop 2023-01-02T00:00:00Z --predicate '_measurement="cpu"'
  ```

- **Backup Data**:
  ```bash
  influx backup /path/to/backup --bucket your-bucket
  ```

- **Restore Data**:
  ```bash
  influx restore /path/to/backup
  ```

---

### **5. Managing Tasks**
- **List Tasks**:
  ```bash
  influx task list
  ```

- **Create a Task**:
  ```bash
  influx task create --file /path/to/task.flux
  ```

- **Delete a Task**:
  ```bash
  influx task delete --task-id your-task-id
  ```

---

## **Step 4: Checking Stored Data**

### **Using the InfluxDB 2.x Web Dashboard**
1. Open your browser and navigate to `http://<your-server-ip>:8086`.
2. Use the **Data Explorer** to write Flux queries and visualize data.
3. Check **Buckets** under **Load Data** to see stored data.

### **Using the CLI**
- **List All Measurements**:
  ```bash
  influx query 'from(bucket: "your-bucket") |> range(start: -1h) |> keys() |> keep(columns: ["_measurement"]) |> distinct()'
  ```

- **List All Fields in a Measurement**:
  ```bash
  influx query 'from(bucket: "your-bucket") |> range(start: -1h) |> filter(fn: (r) => r._measurement == "cpu") |> keys() |> keep(columns: ["_field"]) |> distinct()'
  ```

- **Query Specific Data**:
  ```bash
  influx query 'from(bucket: "your-bucket") |> range(start: -1h) |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage")'
  ```

- **Count Data Points**:
  ```bash
  influx query 'from(bucket: "your-bucket") |> range(start: -1h) |> filter(fn: (r) => r._measurement == "cpu") |> count()'
  ```

---

## **Step 5: Troubleshooting**

- **Check Logs**:
  InfluxDB logs are located at `/var/log/influxdb/`. Use `tail` to monitor logs in real-time:
  ```bash
  tail -f /var/log/influxdb/influxd.log
  ```

- **Check Disk Usage**:
  Ensure your server has enough disk space for InfluxDB data:
  ```bash
  df -h
  ```

- **Check InfluxDB Service Status**:
  ```bash
  sudo systemctl status influxdb
  ```

---

## **Summary**
- Install the correct **InfluxDB 2.x CLI**.
- Configure the CLI with your organization and token.
- Use the provided commands to write, query, and manage data.
- Use the web dashboard for visualization and the CLI for advanced queries and management.

Let me know if you encounter any further issues!
