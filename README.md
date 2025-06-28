# SonarQube + Sonar Scanner - Docker Compose

This project sets up **SonarQube** and the **Sonar Scanner CLI** using Docker Compose, enabling local and automated static code analysis.

## 📦 Contents

- `sonarqube.yml`: Configures the SonarQube service.
- `sonar-scanner.yml`: Configures the CLI client Sonar Scanner, which connects to SonarQube.

## 🚀 Requirements

### Recommended Minimum Hardware

| Resource         | Recommended                    |
|------------------|-------------------------------|
| CPU              | 2 cores                        |
| RAM              | 4 GB free                      |
| Disk Space       | 2 GB minimum (more for large codebases) |

> ⚠️ Running with fewer resources may lead to performance issues or prevent SonarQube from starting.

### Required Software

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/)
- Internet access to download images

---

## 📁 Structure

```
.
├── sonar-scanner.yml
├── sonarqube.yml
└── sonar-project.properties  <-- must be created inside your source project
```

---

## ⚙️ Configuration

### 1. Docker Network

Before starting the services, create the shared network `sonar-network`:

```bash
docker network create sonar-network
```

### 2. Important Variables

#### In `sonar-scanner.yml`

- `SONAR_HOST_URL=http://sonarqube:9000`: The URL of the SonarQube service. Only change if the container name changes.
- `SONAR_LOGIN=<TOKEN>`: 🔒 **Important: replace this with your own SonarQube authentication token**.
- `volumes`: Update `/Users/jugh/dev/portal-ccolon` with the absolute path of the project you want to scan.

```yaml
volumes:
  - /path/to/your/project:/usr/src
```

### 3. Generate SonarQube Token

1. Access SonarQube at [http://localhost:9000](http://localhost:9000).
2. Log in with default credentials:
   - Username: `admin`
   - Password: `admin`
3. Go to **My Account > Security** and generate a new token.
4. Use this token as the value for `SONAR_LOGIN` in `sonar-scanner.yml`.

### 4. Project Configuration - `sonar-project.properties`

Inside your project directory, create a file named `sonar-project.properties` with the following content (adapt as needed):

```properties
sonar.projectKey=your_project_key
sonar.projectName=Your Project Name
sonar.projectVersion=1.0
sonar.sources=.
sonar.sourceEncoding=UTF-8
```

You can include additional properties such as exclusions or language settings if necessary.

---

## ▶️ Usage

### 1. Start SonarQube

```bash
docker compose -f sonarqube.yml up -d
```

Access the dashboard: [http://localhost:9000](http://localhost:9000)

### 2. Run Sonar Scanner

```bash
docker compose -f sonar-scanner.yml run --rm sonar-scanner
```

This will analyze the project in the defined volume and send the report to SonarQube.

---

## 🧠 Additional Notes

- The `sonar_data` and `sonar_extensions` volumes ensure SonarQube persistence across restarts.
- You may merge both YAML files into a single `docker-compose.yml` if preferred.
- The scanner assumes that a valid `sonar-project.properties` file exists in the root directory of your project.

---

## 🛠 Troubleshooting

| Problem                                   | Solution                                                  |
|-------------------------------------------|-----------------------------------------------------------|
| Cannot connect to `sonarqube:9000`       | Ensure both containers are on the same Docker network.    |
| Invalid `SONAR_LOGIN` token              | Generate a new token in the SonarQube web interface.      |
| Permission denied on volume mount        | Ensure Docker has access to the specified host directory. |
| Missing `sonar-project.properties`       | Create the file in your project directory with basic settings. |
