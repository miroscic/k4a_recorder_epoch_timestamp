# k4a_recorder_epoch_timestamp
The k4a recorder provided by the Azure Kinect Sensor SDK that save also a json file containing the epoch timestamp of each frame

## Prerequisites

Before building this project, make sure you have the following installed:

- **Azure Kinect Sensor SDK** (including k4a and k4arecord libraries)
- **CMake** (version 3.16 or higher)
- **A C++ compiler** supporting C++11 or later

### Windows
- Visual Studio 2019 or later with C++ support
- Azure Kinect Sensor SDK installed from Microsoft

### Linux
- GCC or Clang compiler
- Azure Kinect Sensor SDK installed via package manager or built from source

## Building the Project

### Windows

1. Open a Command Prompt or PowerShell
2. Navigate to the project directory:
   ```cmd
   cd path\to\k4a_recorder_epoch_timestamp
   ```
3. Create and navigate to build directory:
   ```cmd
   mkdir build
   cd build
   ```
4. Configure the project with CMake:
   ```cmd
   cmake ..
   ```
5. Build the project:
   ```cmd
   cmake --build . --config Release
   ```

### Linux

1. Open a terminal
2. Navigate to the project directory:
   ```bash
   cd /path/to/k4a_recorder_epoch_timestamp
   ```
3. Create and navigate to build directory:
   ```bash
   mkdir build
   cd build
   ```
4. Configure the project with CMake:
   ```bash
   cmake ..
   ```
5. Build the project:
   ```bash
   make -j$(nproc)
   ```

## Running the Application

### Basic Usage

After successful compilation, you can run the k4arecorder with various options:

```bash
# Windows
./Release/k4arecorder.exe [options] output_file.mkv

# Linux
./k4arecorder [options] output_file.mkv
```

### Command Line Options

The recorder supports various command line options for configuring the Azure Kinect device:

- `--help` - Display help information
- `--list` - List available devices
- `--device <index>` - Specify device index (default: 0)
- `--record-length <seconds>` - Recording duration in seconds
- `--color-mode <mode>` - Color camera mode
- `--depth-mode <mode>` - Depth camera mode
- `--camera-fps <fps>` - Camera frame rate
- `--imu` - Include IMU data in recording
- `--exposure-control <value>` - Manual exposure control
- `--gain <value>` - Manual gain control
- `--external-sync <mode>` - External synchronization mode (Master/Subordinate)

### Example Usage

```bash
# Record for 30 seconds with default settings
k4arecorder --record-length 30 recording.mkv

# Record with specific color and depth modes
k4arecorder --color-mode 1080p --depth-mode WFOV_2X2BINNED --camera-fps 15 recording.mkv

# Record with IMU data included
k4arecorder --imu --record-length 60 recording_with_imu.mkv
```

### Multi-Device Synchronization

When using multiple Azure Kinect devices, you can synchronize them using master-subordinate configuration:

#### Master Device Setup
```bash
# Configure the first device as master (generates sync signals)
k4arecorder --device 0 --external-sync master --record-length 60 master_recording.mkv
```

#### Subordinate Device Setup
```bash
# Configure additional devices as subordinates (receive sync signals)
k4arecorder --device 1 --external-sync sub --record-length 60 subordinate1_recording.mkv
k4arecorder --device 2 --external-sync sub --record-length 60 subordinate2_recording.mkv
```

#### Hardware Connection for Sync
For proper synchronization, connect the devices with sync cables:
- **Master device**: Connect sync-out port to sync-in port of subordinate devices
- **Subordinate devices**: Connect sync-in port to the sync-out of master or daisy-chain from previous subordinate

#### Complete Multi-Device Recording Example
```bash
# Start subordinate devices first (they wait for master sync signal)
k4arecorder --device 1 --external-sync sub --color-mode 720p --depth-mode WFOV_2X2BINNED --camera-fps 15 --record-length 120 device1_sub.mkv &
k4arecorder --device 2 --external-sync sub --color-mode 720p --depth-mode WFOV_2X2BINNED --camera-fps 15 --record-length 120 device2_sub.mkv &

# Start master device last (begins synchronization)
k4arecorder --device 0 --external-sync master --color-mode 720p --depth-mode WFOV_2X2BINNED --camera-fps 15 --record-length 120 device0_master.mkv
```

**Note**: All synchronized devices should use identical camera settings (color-mode, depth-mode, fps) for optimal results.

## Output Files

The application generates two files:
1. **recording.mkv** - The main video recording in Matroska format
2. **recording_timestamps.json** - A JSON file containing epoch timestamps for each frame

## Troubleshooting

### Common Issues

1. **Azure Kinect SDK not found**: Make sure the SDK is properly installed and the environment variables are set correctly.

2. **Device not detected**: 
   - Check if the Azure Kinect device is properly connected
   - Try running with administrator/sudo privileges
   - Use `--list` option to see available devices

3. **Build errors on Linux**: 
   - Ensure you have the development packages installed
   - Check that the Azure Kinect SDK libraries are in the system library path

### Windows Specific
- Make sure the Azure Kinect SDK installation directory is in your PATH
- The required DLLs should be in the same directory as the executable or in PATH

### Linux Specific
- Install the Azure Kinect SDK packages:
  ```bash
  # Ubuntu/Debian
  sudo apt install libk4a1.4-dev libk4arecord1.4-dev
  ```
- Set up udev rules for device access without sudo (refer to Azure Kinect SDK documentation)

## License

This project is licensed under the MIT License - see the LICENSE file for details.
