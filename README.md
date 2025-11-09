#!/bin/bash

Email: 24f3001764@ds.study.iitm.ac.in

# GitHub Actions Matrix Build - Quick Setup Script

echo "========================================="
echo "GitHub Actions Matrix Build Setup"
echo "========================================="
echo ""

# Get user email
read -p "Enter your email address: " USER_EMAIL

# Get repository name
read -p "Enter repository name (default: matrix-build-demo): " REPO_NAME
REPO_NAME=${REPO_NAME:-matrix-build-demo}

echo ""
echo "Setting up repository: $REPO_NAME"
echo "Email: $USER_EMAIL"
echo ""

# Create directory structure
mkdir -p $REPO_NAME/.github/workflows
cd $REPO_NAME

# Create workflow file
cat > .github/workflows/matrix-build.yml << 'WORKFLOW_EOF'
name: Matrix Build with Artifacts

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]
  workflow_dispatch:

jobs:
  matrix-build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
        node-version: [18, 20]
        include:
          - os: ubuntu-latest
            platform: linux
          - os: macos-latest
            platform: macos
          - os: windows-latest
            platform: windows
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - name: matrix-6a2e822
        run: |
          echo "Building for ${{ matrix.platform }} with Node.js ${{ matrix.node-version }}"
          echo "OS: ${{ matrix.os }}"
          echo "Timestamp: $(date)"
        shell: bash
      
      - name: Create build directory
        run: mkdir -p build
        shell: bash
      
      - name: Generate build artifact
        run: |
          echo "Build Information" > build/output.txt
          echo "==================" >> build/output.txt
          echo "Platform: ${{ matrix.platform }}" >> build/output.txt
          echo "OS: ${{ matrix.os }}" >> build/output.txt
          echo "Node Version: ${{ matrix.node-version }}" >> build/output.txt
          echo "Build Time: $(date)" >> build/output.txt
          echo "Job ID: ${{ github.run_id }}-${{ github.run_number }}" >> build/output.txt
          echo "GitHub Actor: ${{ github.actor }}" >> build/output.txt
          echo "" >> build/output.txt
          echo "System Information:" >> build/output.txt
          node --version >> build/output.txt
          npm --version >> build/output.txt
        shell: bash
      
      - name: Create build metadata JSON
        run: |
          cat > build/metadata.json << 'EOF'
          {
            "platform": "${{ matrix.platform }}",
            "os": "${{ matrix.os }}",
            "node": "${{ matrix.node-version }}",
            "timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
            "run_id": "${{ github.run_id }}",
            "workflow": "${{ github.workflow }}",
            "ref": "${{ github.ref }}"
          }
          EOF
        shell: bash
      
      - name: Display build contents
        run: |
          echo "=== Build Output ==="
          cat build/output.txt
          echo ""
          echo "=== Build Metadata ==="
          cat build/metadata.json
        shell: bash
      
      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: build-6a2e822-${{ matrix.platform }}-node${{ matrix.node-version }}
          path: build/
          retention-days: 30
          if-no-files-found: error
  
  summary:
    needs: matrix-build
    runs-on: ubuntu-latest
    steps:
      - name: Build summary
        run: |
          echo "✅ All matrix builds completed successfully!"
          echo "Total builds: 6 (3 platforms × 2 Node versions)"
          echo "Artifacts uploaded with prefix: build-6a2e822"
WORKFLOW_EOF

# Create README.md
cat > README.md << README_EOF
# Matrix Build with Artifacts - DevOps Challenge

## Contact Information
**Email:** $USER_EMAIL

## Overview
This repository demonstrates a GitHub Actions matrix build strategy with artifact management across multiple platforms and Node.js versions.

## Workflow Details

### Matrix Configuration
- **Platforms:** 3 (Ubuntu Linux, macOS, Windows)
- **Node.js Versions:** 2 (18, 20)
- **Total Build Variants:** 6 parallel jobs

### Features
✅ Multi-platform matrix builds (Linux, macOS, Windows)  
✅ Multiple Node.js versions (18, 20)  
✅ Parallel execution of all matrix variants  
✅ Unique artifacts for each build configuration  
✅ Artifact naming: \`build-6a2e822-<platform>-node<version>\`  
✅ Step identifier: \`matrix-6a2e822\` included  

### Artifacts Generated
Each matrix job generates:
1. \`output.txt\` - Build information and system details
2. \`metadata.json\` - Structured build metadata

**Artifact naming convention:**
- \`build-6a2e822-linux-node18\`
- \`build-6a2e822-linux-node20\`
- \`build-6a2e822-macos-node18\`
- \`build-6a2e822-macos-node20\`
- \`build-6a2e822-windows-node18\`
- \`build-6a2e822-windows-node20\`

## Workflow Triggers
- Push to \`main\` or \`master\` branch
- Pull requests to \`main\` or \`master\` branch
- Manual workflow dispatch

## Validation Checklist
- [x] At least 3 successful matrix jobs (we have 6)
- [x] At least 3 artifacts with prefix \`build-6a2e822\` (we have 6)
- [x] All artifacts contain actual content (output.txt + metadata.json)
- [x] Step identifier \`matrix-6a2e822\` present in workflow
- [x] README.md with email address included

## License
MIT License
README_EOF

# Initialize git repository
git init
git add .
git commit -m "Initial commit: Add matrix build workflow with artifacts"

echo ""
echo "========================================="
echo "✅ Repository setup complete!"
echo "========================================="
echo ""
echo "Next steps:"
echo "1. Create a new repository on GitHub named: $REPO_NAME"
echo "2. Run these commands:"
echo ""
echo "   git remote add origin https://github.com/YOUR_USERNAME/$REPO_NAME.git"
echo "   git branch -M main"
echo "   git push -u origin main"
echo ""
echo "3. Go to GitHub Actions tab to see the workflow run"
echo "4. Download artifacts after workflow completes"
echo ""
echo "Repository location: $(pwd)"
echo "========================================="