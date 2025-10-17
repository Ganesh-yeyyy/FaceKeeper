# Digital Attendance System - Face Recognition

## Overview

A Python-based automated attendance system that uses face recognition technology to mark attendance in real-time. The system captures face images during registration, trains a Local Binary Patterns Histograms (LBPH) face recognizer, and then identifies registered users from a webcam feed to automatically log their attendance. It includes duplicate prevention (one entry per person per day), report generation in CSV/Excel formats, and a CLI-based menu interface for all operations.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **CLI Interface**: Menu-driven terminal interface using Python's built-in `input()` for user interactions
- **Display System**: Simple text-based output with Unicode symbols (✓, ✗, 📋) for visual feedback
- **Real-time Video Display**: OpenCV's `cv2.imshow()` for displaying webcam feed during registration and attendance marking

### Backend Architecture

#### Face Recognition Pipeline
- **Face Detection**: Haar Cascade Classifier (`haarcascade_frontalface_default.xml`) for detecting faces in video frames
- **Face Recognition Algorithm**: LBPH (Local Binary Patterns Histograms) Face Recognizer from OpenCV's `cv2.face` module
  - Chosen for its simplicity, speed, and ability to work with grayscale images
  - Recognition threshold set at 70 (0-100 scale, lower is stricter)
- **Training Process**: Captures 30 face samples per user during registration, converts to grayscale, and trains the LBPH model
- **Model Persistence**: Trained recognizer saved to `face_recognizer.yml` file

#### Data Storage
- **Database**: SQLite3 for persistent storage
  - Lightweight, serverless, requires no configuration
  - Two main tables: `users` (id, roll_number, name, created_at) and `attendance` (id, user_id, date, time, status, created_at)
  - UNIQUE constraint on `(user_id, date)` to prevent duplicate attendance entries per day
- **File Storage**: Face images stored in `known_faces/` directory organized by user ID

#### Duplicate Prevention
- **Database-level**: UNIQUE constraint on user_id and date combination in attendance table
- **Runtime Tracking**: In-memory set (`marked_today`) tracks users already marked during current session

#### Report Generation
- **Data Export**: Pandas DataFrames for data manipulation
- **Supported Formats**: CSV (via `to_csv()`) and Excel (via `to_excel()` with openpyxl)
- **Report Types**: Daily reports, date-range reports, and individual user attendance history
- **Storage**: Generated reports saved to `attendance_reports/` directory

#### Modular Design
- **database.py**: All SQLite operations encapsulated in `AttendanceDatabase` class
- **face_register.py**: Face capture and registration in `FaceRegistration` class
- **attendance.py**: Recognition and attendance marking in `AttendanceSystem` class
- **utils/helpers.py**: Shared utility functions (timestamp formatting, validation)
- **utils/report_generator.py**: Report generation logic in `ReportGenerator` class
- **main.py**: Entry point orchestrating all modules

### Authentication and Authorization
- No user authentication implemented - system assumes physical access control
- User identification solely through face recognition during attendance marking

## External Dependencies

### Core Libraries
- **opencv-contrib-python**: Provides both standard OpenCV functionality and additional modules including the face recognition module (`cv2.face.LBPHFaceRecognizer`)
- **numpy**: Array operations for image processing and numerical computations
- **pandas**: Data manipulation and export functionality for report generation
- **openpyxl**: Excel file format support for pandas DataFrame export
- **sqlite3**: Built-in Python library for database operations (no external dependency)

### Hardware Requirements
- **Webcam**: Required for face capture during registration and real-time recognition
- Accessed via `cv2.VideoCapture(0)` - designed to work in Replit environment

### File System Dependencies
- **Haar Cascade Files**: Pre-trained XML file from OpenCV (`haarcascade_frontalface_default.xml`) accessed via `cv2.data.haarcascades`
- **Directory Structure**: Auto-creates `known_faces/`, `attendance_reports/` directories on first run
- **Database File**: `attendance.db` SQLite file auto-created in project root
- **Model File**: `face_recognizer.yml` created after first face registration and training

### Third-party Services
- None - system operates entirely offline and locally