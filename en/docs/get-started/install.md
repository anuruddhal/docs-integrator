---
sidebar_position: 4
title: Install WSO2 Integrator
description: Download and install WSO2 Integrator IDE to build intelligent integrations, APIs, and AI agents across cloud, on-premises, and hybrid environments.
---

# Install WSO2 Integrator

Get your development environment ready to build integrations in under 5 minutes.

WSO2 Integrator is a 100% open source IDE that enables you to connect AI agents, APIs, data, and events across cloud, on-premises, and hybrid environments with a unique low-code experience and pro-code parity.

## Download Options

WSO2 Integrator is available in multiple editions to suit different use cases:

- **WSO2 Integrator** -- Full-featured IDE with low-code and pro-code capabilities
- **WSO2 Integrator: MI** -- Low-code graphical interface for integration development
- **WSO2 Integrator: SI** -- Visual stream flow designer for streaming integrations
- **WSO2 Integrator: ICP** -- Control plane for monitoring and managing deployments

For development, we recommend starting with **WSO2 Integrator** (also called **WSO2 Integrator: BI**).

## System Requirements

Before installation, ensure your system meets the requirements listed on the [System Requirements & Prerequisites](system-requirements.md) page. Here's a quick overview:

- **Operating System:** Windows 10+, macOS 14.6+, or Ubuntu 24.04 LTS and later
- **Memory:** 512 MB minimum (1 GB+ recommended)
- **Disk Space:** 2 GB free space for installation and projects
- **Java:** JRE 21 or later

For detailed information, see [System Requirements & Prerequisites](system-requirements.md).

## Installation Steps

### Step 1: Download WSO2 Integrator

1. Visit the [WSO2 Integrator Downloads page](https://wso2.com/products/downloads/?product=wso2integrator).
2. Select your operating system (Windows, macOS, or Linux).
3. Download the installer for WSO2 Integrator: BI.

### Step 2: Install the IDE

**Windows:**
- Run the `.exe` installer and follow the installation wizard.

**macOS:**
- Run the `.dmg` installer and drag the application to the Applications folder.

**Linux:**
- Extract the `.tar.gz` file and run the startup script:
  ```bash
  tar -xzf wso2-integrator-*.tar.gz
  cd wso2-integrator-*/bin
  ./integrator.sh
  ```

### Step 3: Launch WSO2 Integrator

After installation, launch the IDE:

- **Windows:** Double-click the WSO2 Integrator icon on your desktop or start menu
- **macOS:** Open Applications folder and double-click WSO2 Integrator
- **Linux:** Run the startup script from the `bin` directory

## Verify Installation

After launching WSO2 Integrator:

1. You should see the main IDE interface with the project explorer.
2. Create a new project or open an existing one to verify everything is working.
3. The IDE should display the visual designer and available tools.

## What's Next

- [Create Your First Project](first-project.md) -- Generate a project structure
- [Understand the IDE](understand-the-ide.md) -- Learn the visual designer
- [Quick Start: Integration as API](quick-start-api.md) -- Build your first API integration
