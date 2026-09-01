# Day 2 — Linux Administration

## Overview

Today I practised Linux administration on an Ubuntu AWS EC2 instance.

The session covered:

- System-information commands
- APT package management
- User management
- Group management
- File permissions
- Ownership
- Compression
- Troubleshooting command errors

> Sensitive information such as server addresses, private-key names, and credentials has been removed.

## 1. Connecting to the server

I connected to an Ubuntu EC2 instance using SSH:

```bash
ssh -i "private-key.pem" ubuntu@server-hostname
