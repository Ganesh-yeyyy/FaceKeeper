# Digital Attendance System - Face Recognition

A Python-based automated attendance system that uses face recognition to mark attendance in real-time.

## Features

- **Face Registration**: Capture and register new users with their face images
- **Real-time Recognition**: Automatically recognize faces from webcam feed
- **Attendance Tracking**: Mark attendance with duplicate prevention (one entry per day)
- **Database Management**: SQLite database for storing user profiles and attendance logs
- **Report Generation**: Export attendance reports in CSV or Excel format
- **Statistics Dashboard**: View attendance statistics for all users
- **CLI Interface**: Simple menu-driven interface for all operations

## Tech Stack

- Python 3.11
- OpenCV Contrib (opencv-contrib-python) - Face detection and recognition with LBPH
- NumPy - Numerical operations
- Pandas - Data manipulation and export
- SQLite3 - Database management

## Project Structure

```
/
├── main.py                 # Main entry point with CLI menu
├── database.py             # SQLite database operations
├── face_register.py        # Face registration module
├── attendance.py           # Face recognition and attendance marking
├── utils/
│   ├── helpers.py          # Helper utility functions
│   └── report_generator.py # Report generation module
├── known_faces/            # Stored face images (auto-created)
├── attendance_reports/     # Generated reports (auto-created)
└── attendance.db           # SQLite database (auto-created)
```

## Installation

All required packages are pre-installed:
- opencv-contrib-python (includes face recognition modules)
- numpy
- pandas
- openpyxl

## Usage

Run the main application:
```bash
python main.py
```

### Main Menu Options

1. **Register New Face** - Add a new user to the system
2. **Start Attendance System** - Begin real-time face recognition
3. **View Today's Attendance** - Display attendance for current day
4. **View All Registered Users** - List all registered users
5. **Generate Reports** - Export attendance data to CSV/Excel
6. **View Statistics** - Display attendance statistics
7. **Re-train Face Recognizer** - Rebuild recognition model
8. **Exit** - Close the application

### Registering a New User

1. Select option 1 from the main menu
2. Enter roll number/ID
3. Enter full name
4. Position yourself in front of the webcam
5. The system will capture 30 face samples automatically
6. Face recognizer will be trained with the new data

### Marking Attendance

1. Select option 2 from the main menu
2. Look at the webcam
3. System will automatically detect and recognize faces
4. Attendance is marked when a registered face is recognized
5. Green box = Recognized, Red box = Unknown
6. Press 'q' to quit

### Generating Reports

1. Select option 5 from the main menu
2. Choose report type:
   - Daily report (today's attendance)
   - Full report (all attendance records)
   - User-specific report
3. Choose format (CSV or Excel)
4. Reports are saved in `attendance_reports/` directory

## How It Works

### Face Detection
- Uses Haar Cascade Classifier for fast face detection
- Detects faces in real-time from webcam feed

### Face Recognition
- Uses LBPH (Local Binary Patterns Histograms) Face Recognizer
- Stores face encodings for each registered user
- Matches faces with configurable confidence threshold (70%)

### Attendance Logic
- Marks attendance only once per day per user
- Prevents duplicate entries using database constraints
- Stores date, time, and status for each attendance record

## Database Schema

### Users Table
- `id`: Primary key
- `roll_number`: Unique identifier
- `name`: Full name
- `created_at`: Registration timestamp

### Attendance Table
- `id`: Primary key
- `user_id`: Foreign key to users
- `date`: Attendance date
- `time`: Check-in time
- `status`: Attendance status (Present)
- Unique constraint on (user_id, date)

## Notes

- Webcam is accessed using `cv2.VideoCapture(0)`
- Face samples are stored in `known_faces/` directory
- Trained model is saved as `face_recognizer.yml`
- Database is automatically created on first run
- Reports are timestamped and saved in `attendance_reports/`

## Requirements

- Webcam/camera access
- Good lighting for accurate face detection
- Clear frontal face view for best results

## Troubleshooting

**Camera not working?**
- Ensure webcam is connected and accessible
- Check camera permissions

**Face not being recognized?**
- Ensure good lighting
- Face the camera directly
- Re-register if needed

**Recognition inaccurate?**
- Adjust `recognition_threshold` in `attendance.py`
- Re-train recognizer with more samples
