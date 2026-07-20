# SonarQube + Sonar Scanner - Podman Compose

This project sets up **SonarQube** and the **Sonar Scanner CLI** using Podman Compose, enabling local and automated static code analysis.

## Contents

- `sonarqube.yaml`: Configures the SonarQube service.
- `sonar-scanner.yaml`: Configures the CLI client Sonar Scanner, which connects to SonarQube.

## Requirements

### Required Software

- [Podman](https://podman.io/getting-started/installation)
- [`podman-compose`](https://docs.podman.io/en/latest/markdown/podman-compose.1.html)
- Internet access to download images

---

## Structure

```
.
├── sonar-scanner.yaml
├── sonarqube.yaml
└── sonar-project.properties  <-- must be created inside your source project
```

---

## Configuration

### Important Variables

#### In `sonar-scanner.yaml`

- `SONAR_HOST_URL=http://sonarqube:9000`: The URL of the SonarQube service. Only change if the container name changes.
- `SONAR_LOGIN=<YOUR_SONAR_TOKEN>`: 🔒 **Important: replace this with your own SonarQube authentication token**.
- `volumes`: Update `/path/to/your/project` with the absolute path of the project you want to scan.

```yaml
volumes:
  - /path/to/your/project:/usr/src
```

### Project Configuration - `sonar-project.properties`

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

## Usage

### 1. Start SonarQube

```bash
podman-compose -f sonarqube.yaml up -d
```

This creates the SonarQube container, the `sonar-network` network, and the persistent volumes declared in `sonarqube.yaml`.

Access the dashboard: [http://localhost:9000](http://localhost:9000)

### 2. Generate and configure the token

After SonarQube is running:

1. Access SonarQube at [http://localhost:9000](http://localhost:9000).
2. Log in with the default credentials:
   - Username: `admin`
   - Password: `admin`
3. Go to **My Account > Security** and generate a token.
4. Set that token in `sonar-scanner.yaml` as `SONAR_LOGIN`.

### 3. Run Sonar Scanner

```bash
podman-compose -f sonar-scanner.yaml run --rm sonar-scanner
```

This will analyze the project mounted in `/usr/src` and send the report to SonarQube.

---

## Additional Notes

- The `sonar_data` and `sonar_extensions` volumes are created automatically from `sonarqube.yaml` and ensure SonarQube persistence across restarts.
- You may merge both YAML files into a single `compose.yaml` if preferred.
- The scanner assumes that a valid `sonar-project.properties` file exists in the root directory of your project.

---

## Troubleshooting

| Problem                                   | Solution                                                  |
|-------------------------------------------|-----------------------------------------------------------|
| Cannot connect to `sonarqube:9000`       | Start SonarQube first with `podman-compose -f sonarqube.yaml up -d` so the shared network exists. |
| Invalid `SONAR_LOGIN` token              | Generate a new token in the SonarQube web interface.      |
| Permission denied on volume mount        | Ensure Podman has access to the specified host directory. |
| Missing `sonar-project.properties`       | Create the file in your project directory with basic settings. |
