If you want to set up both **ClickHouse** and **Grafana** using **Docker** on Windows, you can follow these steps to install and run both services in containers. Docker makes it much easier to get both services running with minimal configuration. I'll guide you through the process.

### Step 1: Install Docker Desktop for Windows

If you haven't installed **Docker** yet, here's how to do it:

1. **Download and Install Docker Desktop**:
   - Go to the Docker website: [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop).
   - Follow the installation instructions on the website. Ensure you enable **WSL 2** (Windows Subsystem for Linux) during the installation.
   - After installation, restart your machine if required.
   
2. **Verify Docker Installation**:
   - Open **Command Prompt** or **PowerShell** and run:
     ```bash
     docker --version
     ```
   - You should see the Docker version information if it was installed correctly.

3. **Enable Docker Services**:
   - Docker should start automatically when your computer boots up. If not, open Docker Desktop manually.

### Step 2: Run ClickHouse with Docker

**ClickHouse** is available as a Docker image, and you can easily set it up with the following steps:

1. **Pull the ClickHouse Docker image**:
   - Open **PowerShell** or **Command Prompt** and run:
     ```bash
     docker pull yandex/clickhouse-server
     ```

2. **Run the ClickHouse container**:
   - Now, run the container with the following command:
     ```bash
     docker run -d --name clickhouse-server -p 8123:8123 -p 9000:9000 yandex/clickhouse-server
     ```
   - This command will:
     - Download and run the ClickHouse server in the background.
     - Expose port `8123` for HTTP and `9000` for the native ClickHouse protocol.
     - Name the container `clickhouse-server`.

3. **Verify ClickHouse is Running**:
   - Open your browser and go to [http://localhost:8123](http://localhost:8123). You should see the ClickHouse web interface.
   - To access the ClickHouse client within the container:
     ```bash
     docker exec -it clickhouse-server clickhouse-client
     ```

4. **Optional - Use the ClickHouse web interface**:
   - Go to [http://localhost:8123](http://localhost:8123) to interact with the ClickHouse web interface.

---

### Step 3: Run Grafana with Docker

1. **Pull the Grafana Docker image**:
   - Open **PowerShell** or **Command Prompt** and run:
     ```bash
     docker pull grafana/grafana
     ```

2. **Run the Grafana container**:
   - Now, run Grafana in Docker with the following command:
     ```bash
     docker run -d --name=grafana -p 3000:3000 grafana/grafana
     ```
   - This command will:
     - Run Grafana in the background.
     - Expose port `3000` for accessing the Grafana dashboard in your web browser.

3. **Verify Grafana is Running**:
   - Open your browser and go to [http://localhost:3000](http://localhost:3000).
   - The default login credentials are:
     - **Username**: `admin`
     - **Password**: `admin`
   - After logging in, you’ll be prompted to change the password.

---

### Step 4: Connect Grafana to ClickHouse

To visualize data from ClickHouse in Grafana, you need to set it up as a **data source**.

1. **Open Grafana**:
   - Go to [http://localhost:3000](http://localhost:3000) and log in to Grafana.

2. **Add ClickHouse as a Data Source**:
   - In the Grafana UI, click the **gear icon** (⚙️) on the left sidebar to go to **Configuration**.
   - Click **Data Sources**.
   - Click the **Add Data Source** button.

3. **Select ClickHouse**:
   - In the "Add data source" page, search for **ClickHouse** and select it.
   - Configure the ClickHouse data source:
     - **URL**: `http://localhost:8123` (This is the HTTP interface for ClickHouse).
     - **Database**: You can leave this blank to use the default database or specify a specific one.
     - **Username**: You can leave this blank for default settings or specify one if necessary.
     - **Password**: Leave it blank if no password is set.
   
4. **Test the Connection**:
   - Click **Save & Test** to check if Grafana can successfully connect to the ClickHouse server. If everything is correct, you’ll see a confirmation message.

---

### Step 5: Create Dashboards in Grafana

1. **Create a Dashboard**:
   - In Grafana, click the **"+" icon** in the sidebar, and then select **Dashboard**.
   - Click **Add New Panel** to create a new panel.

2. **Write ClickHouse Queries**:
   - In the panel, choose **ClickHouse** as the data source.
   - You can write queries to interact with your ClickHouse database.
     - Example query:
       ```sql
       SELECT * FROM system.tables LIMIT 10;
       ```
   - Adjust your query as necessary based on the tables and data you have in ClickHouse.

3. **Customize the Dashboard**:
   - You can add multiple panels, visualize the data, and customize the dashboard as needed.

---

### Optional: Using Docker Compose for Simplified Setup

If you prefer to manage both ClickHouse and Grafana together, you can use **Docker Compose** to create and manage the containers easily. Here's an example `docker-compose.yml` file for both services.

1. **Create a `docker-compose.yml` file**:
   - Create a new file named `docker-compose.yml` in your project folder with the following contents:
     ```yaml
     version: '3.7'

     services:
       clickhouse:
         image: yandex/clickhouse-server
         container_name: clickhouse-server
         ports:
           - "8123:8123"
           - "9000:9000"
         volumes:
           - clickhouse-data:/var/lib/clickhouse
         environment:
           - CLICKHOUSE_DB=default

       grafana:
         image: grafana/grafana
         container_name: grafana
         ports:
           - "3000:3000"
         depends_on:
           - clickhouse
         environment:
           - GF_SECURITY_ADMIN_PASSWORD=admin

     volumes:
       clickhouse-data:
         driver: local
     ```

2. **Run Docker Compose**:
   - In the folder where `docker-compose.yml` is located, run:
     ```bash
     docker-compose up -d
     ```

3. **Verify the services**:
   - Grafana should now be available at [http://localhost:3000](http://localhost:3000).
   - ClickHouse will be accessible at [http://localhost:8123](http://localhost:8123).

This method will automatically start both **ClickHouse** and **Grafana** with the correct configuration, making the setup process simpler.

---

### Conclusion

With Docker, it's easy to set up both **ClickHouse** and **Grafana** on Windows without worrying about manual installations. Here's a quick summary:

1. **Install Docker Desktop**.
2. **Run ClickHouse** and **Grafana** containers using `docker run`.
3. **Connect Grafana to ClickHouse** as a data source.
4. Optionally, use **Docker Compose** to run both containers together.

Let me know if you need further assistance with any of the steps!
