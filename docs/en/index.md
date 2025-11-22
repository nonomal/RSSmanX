# RSSman X Documentation

RSSman X is a project that composes several useful services (Tiny Tiny RSS, Mercury, OpenCC, RSSHub, Huginn, etc.) to help you build an excellent RSS experience.

## Quick Start

Follow these simple steps to get RSSman X up and running on your server.

### Prerequisites

- **Docker**: Ensure Docker is installed and running.
- **Docker Compose**: Ensure Docker Compose is installed.

### Installation Steps

1.  **Clone the Repository**
    ```bash
    git clone https://github.com/Colin-XKL/RSSmanX.git
    cd RSSmanX
    ```

2.  **Choose Your Version**
    Navigate to the directory of the version you wish to use:
    - **Lite**: Basic TTRSS and RSSHub.
      ```bash
      cd rssman-lite
      ```
    - **Standard**: Includes TTRSS, RSSHub, Mercury, and more.
      ```bash
      cd rssman-standard
      ```
    - **Ultimate**: Full feature set including Huginn, Global Proxy, and Smart Routing.
      ```bash
      cd rssman-ultimate
      ```

3.  **Configure Environment Variables**
    Each version directory contains a `.env` file (or `.env.example` which you should rename to `.env` if `.env` doesn't exist, though typically it's provided). Open this file and configure the variables.

    *Note: In the current repository structure, the `.env` files are already present.*

    Key variables to check:
    - `DB_Password`: Set a secure password for the database.
    - `HOST`: Your server's IP address or domain name.
    - `TTRSS_PORT`: The port to access TTRSS (e.g., 777).
    - `TTRSS_SELF_URL`: The full URL to access your TTRSS instance (e.g., `http://your-domain.com:777`).

4.  **Run the Application**
    Start the services in the background:
    ```bash
    docker-compose up -d
    ```
    *Note: The first run might take some time to download images.*

5.  **Access the Service**
    Open your browser and visit `http://YOUR_SERVER_IP:777` (or the port you defined).
    - **Default Login**: `admin`
    - **Default Password**: `password`
    - **Important**: Change your password immediately after logging in.

---

## High Level Customization

For advanced users who need more control over the environment and network settings.

### Environment Variables (.env)

The `.env` file is the central place for configuration.

| Variable | Description |
| :--- | :--- |
| `DB_Password` | Password for the PostgreSQL database. |
| `DB_DIR` | Path on the host machine where database files will be stored. Defaults to `/data/docker` or `~/.docker` depending on version. |
| `HOST` | The IP address or domain name of your server. |
| `TTRSS_PORT` | The external port mapped to the TTRSS container. |
| `HUGINN_PORT` | (Ultimate/Standard only) The external port mapped to Huginn. |
| `RSS_ALLOW_PORTS` | Comma-separated list of ports that TTRSS is allowed to connect to for fetching feeds. Default: `80,443,3000,7000`. |
| `TTRSS_SELF_URL` | The public-facing URL of your TTRSS instance. Crucial for correct generation of internal links. |

### Ultimate Version Configuration (config.yaml)

The **Ultimate** version includes a global proxy feature (powered by Clash) to help fetch feeds from blocked sources or to route traffic intelligently. This is configured via `rssman-ultimate/config.yaml`.

**Key Sections:**

- **`proxy-providers`**: Defines where to fetch proxy node lists. You can replace the default URL with your own subscription link.
  ```yaml
  proxy-providers:
    global10:
      type: http
      url: "YOUR_SUBSCRIPTION_URL"
      path: ./merged_sub.yaml
      interval: 3600
  ```

- **`proxy-groups`**: Defines how to select proxies.
  - `fq`: A fallback group that automatically selects an available proxy.
  - `global-random`: Load balances traffic, useful for avoiding anti-crawler mechanisms (e.g., Cloudflare).

- **`rules`**: Defines routing rules.
  - `DOMAIN-KEYWORD,google,fq`: Route Google traffic through the `fq` proxy group.
  - `MATCH,global-random`: Default rule to route unmatched traffic through `global-random`.

**Usage:**
Modify `config.yaml` to add your own proxy servers or adjust routing rules if you encounter issues fetching specific feeds.
