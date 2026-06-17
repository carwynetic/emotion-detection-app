```text
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
```

## `README.md`

````markdown
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
````

Open the app in a browser using the provided localhost URL.

## Run unit tests

```bash
python3 -m unittest test_emotion_detection.py
```

## Run static code analysis

```bash
pylint server.py
```

````

---

## `requirements.txt`

```txt
Flask
requests
pylint
````

---

## `EmotionDetection/__init__.py`

```python
"""EmotionDetection package."""

from .emotion_detection import emotion_detector
```

---

## `EmotionDetection/emotion_detection.py`

```python
"""Emotion detection module using IBM Watson NLP."""

import requests


URL = (
    "https://sn-watson-emotion.labs.skills.network/"
    "v1/watson.runtime.nlp.v1/NlpService/EmotionPredict"
)

HEADERS = {
    "grpc-metadata-mm-model-id": "emotion_aggregated-workflow_lang_en_stock"
}


def emotion_detector(text_to_analyze):
    """Detect emotions from the input text using Watson NLP."""
    payload = {
        "raw_document": {
            "text": text_to_analyze
        }
    }

    response = requests.post(
        URL,
        json=payload,
        headers=HEADERS,
        timeout=30
    )

    if response.status_code == 400:
        return {
            "anger": None,
            "disgust": None,
            "fear": None,
            "joy": None,
            "sadness": None,
            "dominant_emotion": None
        }

    response_json = response.json()
    emotions = response_json["emotionPredictions"][0]["emotion"]

    anger = emotions["anger"]
    disgust = emotions["disgust"]
    fear = emotions["fear"]
    joy = emotions["joy"]
    sadness = emotions["sadness"]

    dominant_emotion = max(emotions, key=emotions.get)

    return {
        "anger": anger,
        "disgust": disgust,
        "fear": fear,
        "joy": joy,
        "sadness": sadness,
        "dominant_emotion": dominant_emotion
    }
```

---

## `test_emotion_detection.py`

```python
"""Unit tests for the emotion detection application."""

import unittest

from EmotionDetection.emotion_detection import emotion_detector


class TestEmotionDetector(unittest.TestCase):
    """Test cases for emotion_detector."""

    def test_emotion_detector(self):
        """Test dominant emotion detection for different input texts."""
        self.assertEqual(
            emotion_detector("I am glad this happened")["dominant_emotion"],
            "joy"
        )

        self.assertEqual(
            emotion_detector("I am really mad about this")["dominant_emotion"],
            "anger"
        )

        self.assertEqual(
            emotion_detector("I feel disgusted just hearing about this")[
                "dominant_emotion"
            ],
            "disgust"
        )

        self.assertEqual(
            emotion_detector("I am so sad about this")["dominant_emotion"],
            "sadness"
        )

        self.assertEqual(
            emotion_detector("I am really afraid that this will happen")[
                "dominant_emotion"
            ],
            "fear"
        )


if __name__ == "__main__":
    unittest.main()
```

---

## `server.py`

```python
"""Flask server for the emotion detection application."""

from flask import Flask, render_template, request

from EmotionDetection.emotion_detection import emotion_detector


app = Flask("Emotion Detector")


@app.route("/")
def render_index_page():
    """Render the index page."""
    return render_template("index.html")


@app.route("/emotionDetector")
def emotion_detector_route():
    """Analyze the input text and return emotion detection results."""
    text_to_analyze = request.args.get("textToAnalyze", "")

    response = emotion_detector(text_to_analyze)

    if response["dominant_emotion"] is None:
        return "Invalid text! Please try again!"

    return (
        "For the given statement, the system response is "
        f"'anger': {response['anger']}, "
        f"'disgust': {response['disgust']}, "
        f"'fear': {response['fear']}, "
        f"'joy': {response['joy']} and "
        f"'sadness': {response['sadness']}. "
        f"The dominant emotion is {response['dominant_emotion']}."
    )


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

## `templates/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Emotion Detection</title>
</head>
<body>
    <h1>Emotion Detection Application</h1>

    <p>Enter text below to analyze the emotion:</p>

    <textarea id="textToAnalyze" rows="5" cols="60"></textarea>
    <br><br>

    <button onclick="runEmotionAnalysis()">Analyze Emotion</button>

    <h2>Result</h2>
    <div id="system_response"></div>

    <script src="{{ url_for('static', filename='mywebscript.js') }}"></script>
</body>
</html>
```

---

## `static/mywebscript.js`

```javascript
function runEmotionAnalysis() {
    const textToAnalyze = document.getElementById("textToAnalyze").value;

    const request = new XMLHttpRequest();

    request.onreadystatechange = function () {
        if (request.readyState === 4 && request.status === 200) {
            document.getElementById("system_response").innerHTML = request.responseText;
        }
    };

    request.open(
        "GET",
        "/emotionDetector?textToAnalyze=" + encodeURIComponent(textToAnalyze),
        true
    );

    request.send();
}
```
