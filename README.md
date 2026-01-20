# Build Visual Studio SSRS Project Release Action

This GitHub Action automates the process of building a Visual Studio SSRS project, packaging build artifacts into a zip file, creating annotated semantic version tags, and publishing a GitHub Release with release notes and attachments. It streamlines the build and release of SSRS (`.rptproj`) projects in a CI pipeline.

---

## Features

- Builds a Visual Studio SSRS project (`.rptproj`) using MSBuild.
- Packages the build output into a versioned zip file.
- Supports both pre-release and standard semantic versioning with a custom suffix.
- Creates an annotated git tag for each new version.
- Attaches the zip file when publishing a GitHub Release.
- Adds a job summary with release details.
- Comments the release status and tag on the associated pull request.
- Includes release date and time in Central Daylight Time (CDT).

---

## Inputs

| Name                | Description                                                                 | Required |
|---------------------|-----------------------------------------------------------------------------|----------|
| `project-file-name` | The name of the Visual Studio SSRS project file (`.rptproj`) to be built.   | Yes      |
| `project-folder-path`| The path to the folder containing the Visual Studio SSRS project file.      | Yes      |
| `prerelease`        | Mark the release as a pre-release (`true`/`false`).                         | Yes      |
| `prerelease-suffix` | Suffix to append to the version number for pre-releases. Non-pre-releases use the latest version for this suffix. | Yes      |
| `configuration`     | The build configuration (e.g., Debug, Release).                             | Yes      |
| `package-name`      | The base name for the zip file containing the build artifacts.               | Yes      |
| `token`             | GitHub token for authentication to create the release.                       | Yes      |

---

## Outputs

| Name              | Description                                                          |
|-------------------|----------------------------------------------------------------------|
| `version-tag`     | The new tag created for the release (e.g., `v1.0.0`).                |
| `version-number`  | The new tag without the "v" prefix (e.g., `1.0.0`).                  |
| `zip-file-name`   | Name of the zip file that contains the build artifacts (e.g., `my-package.1.0.0.zip`). |
| `zip-file-path`   | The full path to the zip file containing the build objects.           |
| `build-status`    | The build/release outcome: Release Created, No Release, or Release Failed. |

---

## Usage

**Important:**

- This action must be run on a runner that supports MSBuild and PowerShell (e.g., `windows-latest`).
- It should be triggered by a pull request that has been closed and merged.
- The runner must have Visual Studio Build Tools installed/configured to build SSRS projects.
- Ensure you specify the required version of MSBuild and set up any SSRS build dependencies as needed.

### Example Workflow

```yaml
name: Build and Release SSRS Project

on:
  pull_request:
    types: [closed]

jobs:
  build-and-release-ssrs:
    if: github.event.pull_request.merged == true
    runs-on: windows-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v6

      - name: Build SSRS Project and Create Release
        uses: lee-lott-actions/build-vsproject-ssrs-release@v1
        with:
          project-file-name: 'MyReportProject.rptproj'
          project-folder-path: 'src/MyReportProject'
          prerelease: 'false'
          prerelease-suffix: 'beta'
          configuration: 'Release'
          package-name: 'my-ssrs-release'
          token: ${{ secrets.GITHUB_TOKEN }}
```

---

## Notes

- Action will proceed only if the PR was merged (`github.event.pull_request.merged == true`).
- Tags and releases follow [semantic versioning](https://semver.org/) with pre-release support.
- Summary of the release (version tag, zip file name, release URL) is added to the job summary.
- For pre-releases, an annotated tag is created but a GitHub Release is not published.
- For standard releases, a tag is created, a zip file is attached, and a GitHub Release is published with notes.

---
