''
emotion-detection-app/
│
├── README.md
├── server.py
├── test_emotion_detection.py
├── requirements.txt
│
├── EmotionDetection/
│   ├── __init__.py
│   └── emotion_detection.py
│
├── templates/
│   └── index.html
│
└── static/
    └── mywebscript.js
''

README.md
# Emotion Detection Application

This project is a Flask web application that uses IBM Watson NLP to detect emotions from text input.

The application analyzes user-provided text and returns emotion scores for anger, disgust, fear, joy, and sadness. It also identifies the dominant emotion.

## Files

- `EmotionDetection/emotion_detection.py`: Contains the emotion detection function.
- `EmotionDetection/__init__.py`: Makes EmotionDetection a Python package.
- `server.py`: Flask web server.
- `test_emotion_detection.py`: Unit tests for the emotion detection function.
- `templates/index.html`: Web interface.
- `static/mywebscript.js`: JavaScript for calling the Flask route.

## Run the application

```bash
python3 server.py

Open the app in a browser using the provided localhost URL.

Run unit tests
python3 -m unittest test_emotion_detection.py
Run static code analysis
pylint server.py
