# ros-bloom-generate

## Description

An action to run [bloom-generate](https://github.com/ros-infrastructure/bloom) command and generate deb files for ROS packages.

- Supported ROS distribution / OS
  - melodic / Ubuntu-18.04
  - noetic / Ubuntu-20.04

- Supported repositories configuration
  - [vcs](https://github.com/dirk-thomas/vcstool)
  - a GitHub repository

- Supported output of deb files
  - workflow artifacts
  - release assets

## Required input

1. targets: Space separated list of directories of package.xml. Path should be relative to the vcs root directory or the target repository root directory.
1. setup_dependencies_script_path: Path of the script to be executed before bloom-generate. rosdep install will be executed in the default script.
1. ros_distro: ROS distribution (noetic or melodic).
1. repository_type: vcs or github
1. save_as_release_assets: Save artifacts as release assets. Default is false.
1. save_as_workflow_artifacts:Save artifacts as workflow artifacts. Default is true.

## Required input for repository_type == vcs

1. vcs_setting_file: Path of the file for vcs import.

## Required input for repository_type == github

1. repository_owner: Owner part of GitHub Repository path.
1. repository: Repository part of GitHub Repository path.
1. ref: Ref of GitHub Repository.

## Required input for save_as_release_assets == true

1. tag_prefix: Prefix of the release tag.
1. gh_token: token for gh (GitHub CLI).

## Required input for save_as_workflow_artifacts == true

1. name_prefix: Prefix of the artifact name.

## Example for repository_type == vcs

```yaml
jobs:
  build:
    runs-on: ubuntu-20.04
    steps:
      - run: |
          echo "repositories:" >./vcs.repos
          echo "  navigation:" >>./vcs.repos
          echo "    type: git" >>./vcs.repos
          echo "    url: https://github.com/smilerobotics/navigation.git" >>./vcs.repos
          echo "    version: noetic-devel" >>./vcs.repos
          echo "  teb_local_planner:" >>./vcs.repos
          echo "    type: git" >>./vcs.repos
          echo "    url: https://github.com/smilerobotics/teb_local_planner" >>./vcs.repos
          echo "    version: melodic-devel" >>./vcs.repos
        shell: bash
      - uses: smilerobotics/actions-ros-bloom-generate@v1
        with:
          repository_type: vcs
          vcs_setting_file: ./vcs.repos
          targets: ./navigation/map_server ./navigation/voxel_grid ./navigation/costmap_2d ./navigation/nav_core ./navigation/navfn ./navigation/base_local_planner ./navigation/carrot_planner ./navigation/dwa_local_planner ./navigation/rotate_recovery ./navigation/fake_localization ./navigation/move_slow_and_clear ./navigation/clear_costmap_recovery ./navigation/global_planner ./navigation/amcl ./navigation/move_base ./navigation/navigation ./teb_local_planner
          ros_distro: noetic
          name_prefix: noetic-
```

## Example for repository_type == github

```yaml
jobs:
  build:
    runs-on: ubuntu-20.04
    steps:
      - uses: smilerobotics/actions-ros-bloom-generate@v1
        with:
          repository_owner: smilerobotics
          repository: rplidar_ros
          ref: reconfigure
          ros_distro: noetic
          targets: ./rplidar_ros
          name_prefix: noetic-
          repository_type: github
```

## Components of this action

- [smilerobotics/actions-vcs-import](https://github.com/smilerobotics/actions-vcs-import)
- [smilerobotics/actions-ros-bloom-generate-noetic](https://github.com/smilerobotics/actions-ros-bloom-generate-noetic)
- [smilerobotics/actions-ros-bloom-generate-melodic](https://github.com/smilerobotics/actions-ros-bloom-generate-melodic)
