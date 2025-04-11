# remote-work-monitor

Creating a complete Python program for monitoring productivity and wellness of remote workers through activity tracking and sentiment analysis involves several components. I'll provide a simplified version of such a system. This program will focus on:

1. Tracking activity using simple input (e.g., keyboard and mouse input).
2. Performing sentiment analysis based on text input (e.g., daily log summaries from workers).

To fully implement this, we will use:
- `pynput` library to monitor keyboard and mouse activity.
- `textblob` for sentiment analysis.

The program will have basic error handling, and detailed comments explaining each part. 

First, you'll need to install the required libraries if they are not already installed. You can install them using:
```bash
pip install pynput textblob
```

Here is the Python program for the remote work monitor:

```python
import logging
import time
from pynput import keyboard, mouse
from textblob import TextBlob

# Configure logging for activity
logging.basicConfig(filename='activity.log', level=logging.DEBUG, format='%(asctime)s - %(message)s')

# Handler to log keyboard activity
def on_key_press(key):
    try:
        logging.info(f'Key pressed: {key.char}')
    except AttributeError:
        logging.info(f'Special Key pressed: {key}')

# Handler to log mouse activity
def on_click(x, y, button, pressed):
    if pressed:
        logging.info(f'Mouse clicked at ({x}, {y}) with {button}')

def on_scroll(x, y, dx, dy):
    logging.info(f'Mouse scrolled at ({x}, {y}) by ({dx}, {dy})')

# Activity Tracker
class ActivityTracker:
    def __init__(self):
        self.keyboard_listener = keyboard.Listener(on_press=on_key_press)
        self.mouse_listener = mouse.Listener(on_click=on_click, on_scroll=on_scroll)

    def start(self):
        # Start the keyboard and mouse listeners
        self.keyboard_listener.start()
        self.mouse_listener.start()

    def stop(self):
        # Stop the listeners
        self.keyboard_listener.stop()
        self.mouse_listener.stop()

# Sentiment Analysis Function
def analyze_sentiment(text):
    try:
        blob = TextBlob(text)
        sentiment = blob.sentiment
        return sentiment.polarity, sentiment.subjectivity
    except Exception as e:
        logging.error(f"Error in sentiment analysis: {str(e)}")
        return None, None

# Main function
def main():
    tracker = ActivityTracker()
    tracker.start()
    
    try:
        print("Activity tracking started. Press Ctrl+C to stop.")
        while True:
            time.sleep(1)  # Run indefinitely until interrupted
        
    except KeyboardInterrupt:
        # Handle the Ctrl+C interruption gracefully
        print("Stopping activity tracking...")
        tracker.stop()
    
    # Example usage of sentiment analysis
    sample_text = input("Enter your daily log for sentiment analysis: ")
    polarity, subjectivity = analyze_sentiment(sample_text)
    if polarity is not None:
        print(f"Sentiment polarity: {polarity}, Subjectivity: {subjectivity}")
    else:
        print("Could not perform sentiment analysis due to an error.")

if __name__ == "__main__":
    main()
```

### Breakdown:

1. **Activity Tracking:**
   - Uses `pynput` to listen to keyboard and mouse events.
   - Logs activity in an `activity.log` file.

2. **Sentiment Analysis:**
   - Utilizes `TextBlob` to perform basic sentiment analysis on text input.
   - Extracts polarity and subjectivity as metrics of sentiment.

3. **Error Handling:**
   - Includes try-except blocks where errors might occur, such as during sentiment analysis.

### Notes:

- The program requires users to input daily logs to perform sentiment analysis.
- Make sure to run this script in a secure and private environment as it logs keyboard inputs.
- In a real-world system, ethical and privacy considerations should be addressed, ensuring users' consent and data protection measures.