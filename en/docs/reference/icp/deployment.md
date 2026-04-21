---
title: Deployment
description: Docker Compose profiles and distribution build reference for the ICP Server.
---

# Deployment

## Distribution

Build the packaged distribution:

```bash
./gradlew build
```

Output: `build/distribution/wso2-integration-control-plane-<version>.zip`

Extract and start the server:

```bash
# Extract
unzip build/distribution/wso2-integration-control-plane-<version>.zip -d build/distribution

# Start — Linux/macOS
./bin/icp.sh

# Start — Windows
bin\icp.bat
```
